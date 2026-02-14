# 1. 기능 개요
noa_run은 구조체 기반 입력으로 NoA 실행을 수행하고 NoAResponse를 반환합니다.

# 2. 도메인 맥락
외부 호출자는 C ABI를 통해 요청 메타, 히스토리, 페르소나를 전달합니다.

# 3. 기능 요구사항
1) meta, history, personas 입력을 수용해야 한다.
2) 실행 결과를 NoAResponse로 반환해야 한다.
3) 오류 발생 시 error 필드를 설정해야 한다.
4) meta에 mix_strategy가 있으면 멀티 모델 합성 실행을 수행해야 한다.

# 4. 입력 정의
- meta: NoARequestMeta*
- history: NoAMessageList*
- personas: NoAPersonaList*

# 5. 출력 정의
- text: 실행 결과 텍스트
- error: 오류 메시지

# 6. 비기능 요구사항
1) 입력 포인터가 null일 때 안전하게 오류를 반환해야 한다.

# 7. 경계 조건 및 제약
1) meta->prompt가 비어 있으면 오류를 반환해야 한다.
2) history/personas가 null일 수 있다.
3) mix_strategy가 유효하지 않으면 오류를 반환해야 한다.
4) mix_strategy 유효성 기준은 NoARequestMeta 정의를 따른다.

# 8. 미정 사항
1) history/personas가 null일 때의 구체 처리 정책
2) 오류 코드 체계
3) mix_strategy의 스키마 및 버전 정책
4) 일부 모델 실패 시 처리 정책

가정:
- mix_strategy는 NoARequestMeta에 포함된다.

[논리적 검증 결과]
- 누락 항목: 오류 코드 체계, null 처리 정책, mix_strategy 스키마
- 충돌 항목: 없음
- 모호한 항목: history/personas 필수 여부, 부분 실패 처리 기준

## 예시

```javascript
const meta = { prompt: "질문", system_prompt: "", model: "", base_url: "", mix_strategy: "{...}" };
const history = { items: [message1, message2], count: 2 };
const personas = { items: [persona1, persona2], count: 2 };
const result = noa_run(meta, history, personas);
```
