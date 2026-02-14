# 1. 기능 개요
NoAMessage는 단일 메시지 항목을 표현하는 데이터 구조입니다.

# 2. 도메인 맥락
NoA 요청의 history 배열을 구성하는 최소 단위로 사용됩니다.

# 3. 기능 요구사항
1) role과 content를 포함해야 한다.
2) role은 메시지 발화 주체를 구분해야 한다.

# 4. 입력 정의
- role: string
- content: string

# 5. 출력 정의
- 반환 시 동일한 필드 구조를 유지한다.

# 6. 비기능 요구사항
1) 문자열은 UTF-8 인코딩이어야 한다.

# 7. 경계 조건 및 제약
1) role이 비어 있으면 유효성 오류로 처리해야 한다.
2) content가 비어 있으면 유효성 오류로 처리할 수 있다.

# 8. 미정 사항
1) role의 허용 값 집합 정의
2) content 길이 제한 정책

[논리적 검증 결과]
- 누락 항목: role 허용 값, content 길이 제한
- 충돌 항목: 없음
- 모호한 항목: 빈 content 처리 규칙

## 예시

```javascript
const message = { role: "user", content: "요청 내용" };
```
