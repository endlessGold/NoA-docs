# 1. 기능 개요
json_string_value는 JSON 문자열에서 지정 키의 문자열 값을 추출합니다.

# 2. 도메인 맥락
noa_run_json에서 입력 JSON의 핵심 필드를 파싱하기 위해 사용됩니다.

# 3. 기능 요구사항
1) 키에 해당하는 문자열 값을 찾아 반환해야 한다.
2) 찾지 못하면 null을 반환해야 한다.

# 4. 입력 정의
- json: const char*
- key: const char*

# 5. 출력 정의
- return: char* (추출된 문자열 또는 null)

# 6. 비기능 요구사항
1) 파싱 실패 시 null을 반환해야 한다.

# 7. 경계 조건 및 제약
1) json 또는 key가 null이면 null을 반환해야 한다.
2) 문자열 이스케이프 시퀀스를 처리해야 한다.

# 8. 미정 사항
1) 중첩 JSON 지원 여부
2) 공백/개행 처리 정책

[논리적 검증 결과]
- 누락 항목: 중첩 JSON 지원 범위
- 충돌 항목: 없음
- 모호한 항목: 공백 처리 규칙

## 예시

```javascript
const value = json_string_value(json_input, "prompt");
```
