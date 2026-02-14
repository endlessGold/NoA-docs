# 1. 기능 개요
NoAPersona는 페르소나별 인증 및 시스템 프롬프트를 전달하는 구조체입니다.

# 2. 도메인 맥락
NoA 요청에서 다중 에이전트 실행 시 페르소나별 설정을 제공하기 위해 사용됩니다.

# 3. 기능 요구사항
1) name, api_key, system_prompt를 포함해야 한다.
2) name은 페르소나 식별자로 사용된다.

# 4. 입력 정의
- name: string
- api_key: string
- system_prompt: string

# 5. 출력 정의
- 반환 시 동일한 필드 구조를 유지한다.

# 6. 비기능 요구사항
1) api_key는 외부로 노출되어서는 안 된다.

# 7. 경계 조건 및 제약
1) name이 비어 있으면 유효성 오류로 처리해야 한다.
2) api_key가 비어 있으면 인증 오류로 처리할 수 있다.

# 8. 미정 사항
1) name 중복 허용 여부
2) system_prompt 길이 제한

[논리적 검증 결과]
- 누락 항목: name 중복 정책, system_prompt 길이 제한
- 충돌 항목: 없음
- 모호한 항목: api_key 미입력 시 동작

## 예시

```js
persona = { name: "fact_check", api_key: "key", system_prompt: "검증 규칙" }
```
