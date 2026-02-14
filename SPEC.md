# 1. 기능 개요
NoA는 다중 에이전트 기반 응답 합성을 제공하는 C 런타임이며, 외부 호출자는 C ABI 또는 JSON ABI로 동일한 계약을 사용해야 한다.

# 2. 도메인 맥락
- NoA는 다중 관점 제안과 통합을 통해 응답 품질과 안정성을 높여야 한다.
- 호출자는 프롬프트, 히스토리, 시스템 프롬프트, 모델, 베이스 URL, API 키, 페르소나 키를 제공할 수 있어야 한다.
- 런타임은 단일 요청에 대해 다중 에이전트 실행을 조율해야 한다.
- 기본 실행 흐름은 관찰→문제 정의→가설 설정→실험 수행→데이터 분석→결론 도출/공유 순서를 충족해야 한다.

# 3. 기능 요구사항
1) 단일 요청 입력을 받아 최종 응답을 생성해야 한다.
2) 입력은 구조체 기반과 JSON 기반을 모두 수용해야 한다.
3) 요청에는 프롬프트, 히스토리, 시스템 프롬프트, 모델, 베이스 URL, API 키, 페르소나 키가 포함될 수 있어야 한다.
4) 응답은 항상 텍스트와 오류 정보를 포함해야 한다.
5) 오류는 원인 식별이 가능한 형태로 반환되어야 한다.
6) 응답 메모리 소유권과 해제 규칙이 명확해야 한다.
7) 입력 검증 실패, 인증 실패, 네트워크 실패, 내부 처리 실패를 구분 가능한 오류로 분류해야 한다.
8) 멀티 에이전트의 병렬 실행을 지원해야 한다.
9) 실행 제한(동시성, 타임아웃, 최대 히스토리 길이)을 제어할 수 있어야 한다.
10) 새로운 에이전트 타입 및 합성 전략을 확장 가능해야 한다.

# 4. 입력 정의
## 4.1 구조체 기반 입력
- 엔티티: RequestMeta, MessageList, PersonaList
- 필수 필드
  - prompt: 사용자 요청 텍스트
- 선택 필드
  - system_prompt: 시스템 정책 프롬프트
  - model: 모델 식별자
  - base_url: 호출 대상 API 베이스 URL
  - api_key: 기본 API 키
  - history: 대화 히스토리 목록
  - personas: 페르소나 목록

가정: 구조체 기반 API는 단일 요청당 1개의 prompt를 필수로 한다.

## 4.2 JSON 기반 입력
- 엔티티: RequestJson
- 필수 필드
  - prompt: string
- 선택 필드
  - history: array
  - systemPrompt: string
  - model: string
  - baseUrl: string
  - apiKey: string
  - personaApiKeys: object
  - constraints: object

### history 항목 제약
- role: user | assistant | system
- content: string

### constraints 예시 필드
- maxTokens: number
- maxHistoryItems: number
- timeoutMs: number
- concurrency: number

가정: constraints는 선택 필드이며, 미지정 시 런타임 기본값을 사용한다.

# 5. 출력 정의
## 5.1 구조체 기반 출력
- text: 응답 텍스트
- error: 오류 메시지

## 5.2 JSON 기반 출력
- text: string
- error: string
- meta: object

### meta 예시 필드
- usedModel: string
- tokensIn: number
- tokensOut: number
- latencyMs: number
- agentReports: object

가정: meta는 확장 필드이며, 미지정 시 생략 가능하다.

# 6. 비기능 요구사항
1) 안정성: 입력 검증 실패 시에도 프로세스가 중단되지 않아야 한다.
2) 보안: API 키 및 민감 정보는 로그로 출력하지 않아야 한다.
3) 성능: 단일 요청 처리 지연을 측정 가능해야 한다.
4) 확장성: 에이전트 수 증가에 따른 병렬 실행을 지원해야 한다.
5) 이식성: C ABI는 플랫폼 의존성을 최소화해야 한다.
6) 관측성: 오류 코드와 메시지로 장애 원인을 판별 가능해야 한다.

# 7. 경계 조건 및 제약
1) prompt가 비어있거나 null이면 즉시 오류를 반환해야 한다.
2) history 항목 수가 제한을 초과하면 오류 또는 잘라내기 정책을 적용해야 한다.
3) API 키가 없고 외부 호출이 필요한 경우 인증 오류를 반환해야 한다.
4) JSON 입력이 유효하지 않으면 파싱 오류를 반환해야 한다.
5) 반환 문자열은 UTF-8 인코딩이어야 한다.
6) 반환 메모리는 호출자가 해제해야 한다.

# 8. 미정 사항
1) 기본 모델 식별자 및 기본 base_url은 무엇인가?
2) 병렬 실행 최대 동시성의 기본값은 무엇인가?
3) timeout 기본값 및 타임아웃 처리 방식은 무엇인가?
4) history 길이 제한 및 trimming 정책은 무엇인가?
5) meta 필드의 필수/선택 범위를 어떻게 정의할 것인가?
6) 오류 코드 체계를 문자열/숫자 중 어떤 형식으로 표준화할 것인가?

[논리적 검증 결과]
- 누락 항목: 오류 코드 체계 정의, 기본값 정책
- 충돌 항목: 없음
- 모호한 항목: constraints 기본값과 정책 적용 방식

# 9. 설계 개선 제안 (Optional)
## 제안 1
- 목적: 오류 응답의 표준화 및 클라이언트 호환성 개선
- 기대 효과: 오류 처리 자동화 및 디버깅 효율 향상
- 복잡도 증가 수준: medium
- 계약 변경 필요 여부: yes (error 필드의 구조화 필요)
