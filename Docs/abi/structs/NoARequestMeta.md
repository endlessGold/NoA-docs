# 1. 기능 개요
NoARequestMeta는 요청의 핵심 메타데이터를 전달하는 구조체입니다.

# 2. 도메인 맥락
NoA 실행 시 모델, 시스템 프롬프트, 베이스 URL을 제공하기 위해 사용됩니다.
멀티 모델 합성 전략을 전달할 수 있습니다.

# 3. 기능 요구사항
1) prompt 필드를 포함해야 한다.
2) system_prompt, model, base_url은 선택적으로 제공될 수 있다.
3) mix_strategy는 선택적으로 제공될 수 있다.
4) mix_strategy는 지정된 키 집합과 값 형식을 따라야 한다.

# 4. 입력 정의
- prompt: string
- system_prompt: string
- model: string
- base_url: string
- mix_strategy: string (JSON 문자열, 키: proposer_1, proposer_2, reviewer_1, reviewer_2)

# 5. 출력 정의
- 반환 시 동일한 필드 구조를 유지한다.

# 6. 비기능 요구사항
1) 문자열은 UTF-8 인코딩이어야 한다.

# 7. 경계 조건 및 제약
1) prompt가 비어 있으면 유효성 오류로 처리해야 한다.
2) mix_strategy가 제공되면 파싱 가능한 형식이어야 한다.
3) mix_strategy는 proposer_1, proposer_2, reviewer_1, reviewer_2 키를 모두 포함해야 한다.
4) mix_strategy 각 값은 정수 문자열로 파싱 가능해야 한다.
5) 제공되는 설정 UI는 합계가 100 이하가 되도록 제한한다.

# 8. 미정 사항
1) model 기본값 정책
2) base_url 기본값 정책
3) mix_strategy 합계 기준과 서버 검증 정책
4) mix_strategy 지원 모델 확장 및 버전 정책
5) mix_strategy가 없을 때 단일 모델 처리 정책

[논리적 검증 결과]
- 누락 항목: model/base_url 기본값 정책, mix_strategy 합계 기준
- 충돌 항목: 없음
- 모호한 항목: system_prompt 필수 여부, mix_strategy 합계 처리

## 예시

```javascript
const mix_strategy = JSON.stringify({
  proposer_1: "40",
  proposer_2: "30",
  reviewer_1: "20",
  reviewer_2: "10"
});
const meta = { 
  prompt: "질문", 
  system_prompt: "", 
  model: "", 
  base_url: "", 
  mix_strategy 
};
```
