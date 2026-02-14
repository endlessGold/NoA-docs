# 1. 기능 개요
noa_run_json은 JSON 문자열 입력을 처리해 JSON 문자열 결과를 반환합니다.

# 2. 도메인 맥락
구조체 기반 호출이 어려운 환경에서 문자열 기반 ABI를 제공합니다.

# 3. 기능 요구사항
1) UTF-8 JSON 입력을 받아야 한다.
2) JSON 출력 문자열 포인터를 반환해야 한다.
3) 반환 포인터는 호출자가 해제할 수 있어야 한다.
4) JSON 입력에 mix_strategy가 포함되면 멀티 모델 합성을 수행해야 한다.

# 4. 입력 정의
- input_json: const char*

# 5. 출력 정의
- return: char* (JSON 문자열)

# 6. 비기능 요구사항
1) 파싱 실패 시에도 JSON 형식 오류 응답을 반환해야 한다.

# 7. 경계 조건 및 제약
1) input_json이 null이면 오류를 반환해야 한다.
2) prompt 누락 시 오류를 반환해야 한다.
3) 반환 메모리는 noa_free로 해제해야 한다.
4) mix_strategy가 유효하지 않으면 오류를 반환해야 한다.
5) mix_strategy 유효성 기준은 NoARequestMeta 정의를 따른다.

# 8. 미정 사항
1) JSON 스키마의 버전 관리 방식
2) 오류 메시지 표준 포맷
3) mix_strategy JSON 스키마 정의
4) 일부 모델 실패 시 처리 정책

가정:
- mix_strategy는 JSON 입력에 포함된다.

[논리적 검증 결과]
- 누락 항목: JSON 스키마 버전 정책, 오류 포맷, mix_strategy 스키마
- 충돌 항목: 없음
- 모호한 항목: 파싱 실패 시 오류 메시지 형식, 부분 실패 처리 기준

## 예시

```javascript
const input_json = JSON.stringify({ prompt: "질문", mix_strategy: "{...}" });
const output_json = noa_run_json(input_json);
```
