# 1. 기능 개요
NoAMessageList는 메시지 목록과 개수를 전달하는 컨테이너입니다.

# 2. 도메인 맥락
NoA 요청의 대화 히스토리 입력으로 사용됩니다.

# 3. 기능 요구사항
1) items 포인터와 count를 포함해야 한다.
2) count는 items의 길이를 나타내야 한다.

# 4. 입력 정의
- items: NoAMessage* 배열
- count: size_t

# 5. 출력 정의
- 반환 시 동일한 필드 구조를 유지한다.

# 6. 비기능 요구사항
1) count는 플랫폼의 size_t 범위를 준수해야 한다.

# 7. 경계 조건 및 제약
1) count가 0일 때 items는 null일 수 있다.
2) count가 0보다 크면 items는 유효한 메모리를 가리켜야 한다.

# 8. 미정 사항
1) 최대 히스토리 길이 제한
2) 히스토리 초과 시 처리 정책

[논리적 검증 결과]
- 누락 항목: 최대 길이, 초과 처리 정책
- 충돌 항목: 없음
- 모호한 항목: count와 items의 null 허용 범위

## 예시

```javascript
const items = [message1, message2];
const list = { items, count: items.length };
```
