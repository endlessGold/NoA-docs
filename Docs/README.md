# NoA Docs

## 빠른 이동
- 프로젝트 소개: [README.md](../README.md)
- 기능 요구사항(SPEC): [SPEC.md](../SPEC.md)
- API 설계: [API.md](../API.md)
- 문서 공개/동기화: [sync-docs](./build/sync-docs.md)
- 위키 자동 동기화: [sync-wiki](./build/sync-wiki.md)
- GitBook 동기화: [gitbook-sync](./build/gitbook-sync.md)
- ABI: [Structs](./abi/structs/NoAMessage.md), [Functions](./abi/functions/noa_run.md)
- Build: [build-wasm](./build/build-wasm.ps1.md), [ConvertTo-WslPath](./build/ConvertTo-WslPath.md)
- Runtime: [internal/main](./runtime/internal/main.md)

## 프로젝트 목적
- NoA는 Node of Agents의 약자다.
- 프로토타입에서는 동일 모델에 서로 다른 페르소나와 시스템 프롬프트 조건을 부여해 협업 결과를 개선하는 것을 목표로 한다.
- **NoA(Node of Agents) 기반 다중 LLM 협업 추론**: 여러 개의 대형 언어 모델을 단순히 나열하는 것이 아니라, 각 모델이 전문성(예: 창의성·사실성·코드·한국어)에 따라 계층적·병렬적으로 역할을 분담하도록 orchestration 한다.
  - 1층(Proposer): proposer-1, proposer-2, proposer-3 등 역할 에이전트가 동일한 질문에 대해 독립적으로 답변을 생성한다.
  - 2층(Reviewer): reviewer-1, reviewer-2가 1층 출력물을 비교·검증하며 오류·홀루시네이션·논리적 비약을 필터링한다.
  - 3층(Fusion): fusion 에이전트가 앞선 응답들을 종합하여 최종 답변을 만들고, 근거(출처 에이전트·근거 문장)를 함께 제공한다.
  - 선택적 4층(Reflection): 사용자 피드백을 반영해 전체 파이프라인을 재실행하거나 특정 층만 교체할 수 있다.

- **NoA 동작 패턴(과학자 6단계)**: 관찰 → 문제 정의 → 가설 설정 → 실험 수행 → 데이터 분석 → 결론 도출/공유

- **C ABI / JSON ABI 표준화**: 위 협업 추론 파이프라인을 단일 함수(`noa_run`, `noa_run_json`)로 캡슐화하여, Python·Node·Rust·Go·C++ 등 어떤 언어에서도 동일한 인터페이스로 호출할 수 있게 한다.
  - 입력: `NoARequestMeta`에 다층 에이전트 구성(층 수·모델명·temperature·max_tokens·stop sequences·투표 방식)을 JSON으로 기술.
  - 출력: `NoAResponse`에 최종 응답·사용 토큰·각 층별 소요 시간·개별 모델 결과·근거 메타데이터를 담아 반환.
  - 메모리 관리: `noa_free`로 Rust 기반 런타임이 할당한 문자열·구조체를 안전하게 해제.

- **구조화된 메시지 히스토리 & 페르소나**:
  - `NoAMessageList`는 시스템·사용자·보조·모델 간 내부 메시지를 모두 저장하며, 각 메시지에 생성 모델·생성 시점·토큰 비용·신뢰도 점수를 기록.
  - `NoAPersonaList`는 “한국어 법률 전문가”, “보안 침투 테스터”, “친절한 고객 지원”처럼 역할별 프롬프트 세트를 관리하고, 실행 시점에 동적으로 페르소나를 조합·오버라이드할 수 있다.

- **응답 합성 전략**:
  - `best-of-n`: n개 모델 중 단순 최고 확률 응답 선택.
  - `consensus@k`: k개 이상이 동일한 결론에 도달해야 채택.
  - `self-consistency-chain-of-thought`: 여러 추론 경로를 샘플링해 다수결로 최종 답변 결정.
  - `reward-model-rank`: 별도 보상 모델이 각 응답에 점수를 매겨 최고 득점 응답 선정.
  - 사용자는 `NoARequestMeta.strategy` 필드에 위 키워드를 기입하면 즉시 전략이 적용된다.

- **외부 도구 연동**:
  - 외부 함수(웹 검색·내부 지식 DB·파일 시스템)를 `tools` 배열로 선언하면, 1층 Proposer가 필요 시 자동으로 호출하고 결과를 2층 Reviewer에게 전달.
  - 도구 호출 기록도 메시지 히스토리에 누적되어 추후 감사·재현이 가능.

- **확장성 & 이식성**:
  - 전체 런타임을 WebAssembly로 컴파일(`build-wasm.ps1`)하여 브라우저·Edge 함수·IoT 디바이스에서도 동일한 NoA 추론 실행.
  - 로컬 GPU 서버에서는 CUDA 백엔드, 클라우드에서는 OpenAI·Anthropic·Google API를 동시에 연결해 하이브리드 추론.
  - 모든 모델 호출은 비동기·스트리밍 지원으로, 대규모 배치 시에도 전체 파이프라인 완료 시간을 최소화.

- C ABI와 JSON ABI를 통해 NoA 실행을 표준화하고, 외부 환경에서 일관된 호출과 결과 처리를 가능하게 한다.
- 메시지 히스토리, 페르소나, 모델 설정을 구조화해 다중 에이전트 실행과 응답 합성을 지원한다.

## 프로토타입 계획

- 동일 모델을 다중 페르소나로 분리해 서로 다른 시스템 프롬프트 조건을 제공한다.
- 페르소나별 역할과 검증 기준을 정의해 상호 보완 구조를 만든다.
- 동일 입력에 대해 페르소나 조합별 결과 차이를 비교 가능하게 기록한다.
- 협업 결과의 개선 여부를 검증할 평가 기준을 설정한다.

## 예시

```javascript
const input = { messages, mix_strategy, chat_id };
const mix = parse_mix_strategy(input.mix_strategy);

const results = await Promise.all([
  run_agent("proposer_1", input.messages),
  run_agent("proposer_2", input.messages),
  run_agent("reviewer_1", input.messages),
  run_agent("reviewer_2", input.messages)
]);

const prompt = build_combination_prompt(mix, results);
const final = stream_agent("fusion", prompt);
on_finish(final, () => save_history(input.chat_id, input.messages, final));
return final;
```

## ABI

- Structs
    - [NoAMessage](./abi/structs/NoAMessage.md)
    - [NoAMessageList](./abi/structs/NoAMessageList.md)
    - [NoAPersona](./abi/structs/NoAPersona.md)
    - [NoAPersonaList](./abi/structs/NoAPersonaList.md)
    - [NoARequestMeta](./abi/structs/NoARequestMeta.md)
    - [NoAResponse](./abi/structs/NoAResponse.md)

- Notes
    - 멀티 LLM 합성 전략은 NoARequestMeta에 정의됨
    - 합성 전략 키: proposer_1, proposer_2, reviewer_1, reviewer_2

- Functions
    - [noa_run](./abi/functions/noa_run.md)
    - [noa_run_json](./abi/functions/noa_run_json.md)
    - [noa_free](./abi/functions/noa_free.md)

## 에이전트 레지스트리 API
- 목적: 역할 에이전트의 등록·해제·조회·목록화를 지원한다.
- 범위: 상위 오케스트레이터 모듈에서 메타정보를 관리한다.

- **agent_registry_create**
  - 입력: 없음
  - 출력: `AgentRegistryHandle*` (실패 시 null)
- **agent_registry_destroy**
  - 입력: `AgentRegistryHandle* handle`
- **agent_register**
  - 입력: `AgentRegistryHandle* handle`, `agent_id`, `role`, `endpoint`, `metadata`
  - 출력: `bool`
- **agent_unregister**
  - 입력: `AgentRegistryHandle* handle`, `agent_id`
  - 출력: `bool`
- **agent_get**
  - 입력: `AgentRegistryHandle* handle`, `agent_id`
  - 출력: `AgentInfo*` 또는 null
- **agent_list**
  - 입력: `AgentRegistryHandle* handle`
  - 출력: `AgentInfoList*` 또는 null

가정:
- `agent_id`는 전역 유일 식별자다.
- `role`은 Proposer/Reviewer/Fusion/Reflection/MemoryManager 등 역할 명칭을 사용한다.
- `endpoint`는 호출 가능한 주소 또는 핸들 참조다.
- `metadata`는 문자열 직렬화로 전달되며 호출자가 해제 책임을 가진다.

## Runtime

- Internal Functions
    - [clone_string](./runtime/internal/clone_string.md)
    - [json_escape](./runtime/internal/json_escape.md)
    - [json_string_value](./runtime/internal/json_string_value.md)
    - [build_response](./runtime/internal/build_response.md)
    - [read_all_stdin](./runtime/internal/read_all_stdin.md)
    - [main](./runtime/internal/main.md)

## Build

- [build-wasm.ps1](./build/build-wasm.ps1.md)
- [ConvertTo-WslPath](./build/ConvertTo-WslPath.md)
