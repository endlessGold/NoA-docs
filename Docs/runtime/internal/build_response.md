# 1. 기능 개요
build_response는 text와 error를 포함하는 JSON 문자열을 생성합니다.

# 2. 도메인 맥락
NoA 런타임이 표준 JSON 응답을 반환하기 위해 사용됩니다.

# 3. 기능 요구사항
1) text와 error를 JSON 문자열로 직렬화해야 한다.
2) 문자열은 JSON 이스케이프 규칙을 준수해야 한다.

# 4. 입력 정의
- text: const char*
- error: const char*

# 5. 출력 정의
- return: char* (JSON 문자열 또는 null)

# 6. 비기능 요구사항
1) 메모리 할당 실패 시 null을 반환해야 한다.

# 7. 경계 조건 및 제약
1) text 또는 error가 null일 경우 빈 문자열로 처리해야 한다.

# 8. 미정 사항
1) meta 확장 필드 지원 여부

[논리적 검증 결과]
- 누락 항목: meta 확장 정책
- 충돌 항목: 없음
- 모호한 항목: null 입력 처리 규칙

## 예시

```javascript
const json_output = build_response("합성 결과", "");
```
