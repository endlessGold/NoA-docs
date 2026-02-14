# 1. 기능 개요
이 문서는 NoA C 기반 ABI의 외부 API 계약을 정의합니다. 구조체 기반 호출과 JSON 문자열 기반 호출의 입력/출력, 오류 처리, 메모리 소유권을 규정합니다.

# 2. 도메인 맥락
- 호출자는 C ABI를 통해 NoA 런타임에 요청을 전달한다.
- 런타임은 다중 에이전트 실행과 최종 응답 합성을 수행한다.
- 결과는 텍스트와 오류 정보를 표준 형식으로 반환한다.

# 3. 기능 요구사항
1) 구조체 기반 호출을 지원해야 한다.
2) JSON 문자열 기반 호출을 지원해야 한다.
3) 반환 문자열의 메모리 해제 규칙을 제공해야 한다.
4) 오류는 일관된 형식으로 전달되어야 한다.
5) 호출자는 입력 유효성 실패를 식별할 수 있어야 한다.

# 4. 입력 정의
## 4.1 구조체 기반 입력
### 요청 메타
- prompt: string (필수)
- system_prompt: string (선택)
- model: string (선택)
- base_url: string (선택)

### 히스토리
- role: user | assistant | system
- content: string

### 페르소나
- name: string
- api_key: string
- system_prompt: string

가정: 페르소나 리스트는 이름 중복을 허용하지 않는다.

## 4.2 JSON 기반 입력
- prompt: string (필수)
- history: array (선택)
- systemPrompt: string (선택)
- model: string (선택)
- baseUrl: string (선택)
- apiKey: string (선택)
- personaApiKeys: object (선택)
- constraints: object (선택)

가정: JSON 입력은 UTF-8 인코딩된 문자열이다.

# 5. 출력 정의
## 5.1 구조체 기반 출력
- text: string
- error: string

## 5.2 JSON 기반 출력
- text: string
- error: string
- meta: object (선택)

### 오류 분류
- InputError: 입력 파싱 또는 필수 필드 누락
- AuthError: 인증 실패 또는 API 키 누락
- NetworkError: 외부 호출 실패
- RuntimeError: 내부 처리 실패

가정: error 필드는 오류 유형과 메시지를 결합한 문자열이다.

# 6. 비기능 요구사항
1) 호출 중단 없이 오류가 반환되어야 한다.
2) 메모리 누수가 발생하지 않아야 한다.
3) 동일 입력에 대해 결정론적 결과가 반환되어야 한다.
4) API 키는 외부로 노출되지 않아야 한다.

# 7. 경계 조건 및 제약
1) prompt가 비어있으면 InputError를 반환한다.
2) history role이 허용 값이 아니면 InputError를 반환한다.
3) JSON이 파싱 불가하면 InputError를 반환한다.
4) 반환 문자열은 호출자가 해제해야 한다.

# 8. 미정 사항
1) error 문자열의 표준 포맷은 무엇인가?
2) meta 필드에 포함할 최소 항목은 무엇인가?
3) JSON 입력에서 persona 정의 형식은 object로 고정할 것인가?

[논리적 검증 결과]
- 누락 항목: error 포맷 정의, meta 최소 항목
- 충돌 항목: 없음
- 모호한 항목: personaApiKeys 적용 범위
