# Wiki 자동 동기화 (NoA → GitHub Wiki)

이 문서는 비공개 저장소 `NoA`의 문서를 GitHub Wiki로 자동 동기화하는 방법을 설명합니다. 공개 레포를 추가로 만들지 않고도 “Wiki 기반 문서 탐색”을 운영할 수 있습니다.

## 동작 방식
- 트리거: `NoA` 저장소의 `main` 브랜치에 push
- 동기화 대상(위키 페이지):
  - `Home.md`: 위키 랜딩/인덱스(워크플로가 생성)
  - `SPEC.md`: 기능 요구사항
  - `API.md`: API 설계
  - `Docs.md`: Docs 목차(워크플로가 생성)
- 위키 저장소: `https://github.com/<owner>/<repo>.wiki.git`

## 필요한 권한(봇)
GitHub Actions가 위키 저장소에 push 할 수 있어야 합니다.

### 옵션 A: 봇 계정 + PAT (간단)
1) 봇 계정 생성(예: `noa-wiki-bot`)
2) `NoA` 저장소에 Collaborator로 초대(Write 권한)
3) 봇 계정으로 PAT 생성
   - 권한: `repo`
4) `NoA` 저장소 Secrets 등록
   - `NOA_WIKI_TOKEN`: 위 PAT 값

### 옵션 B: GitHub App (권장, 개인 PAT 회피)
1) GitHub App 생성 및 `NoA`에 설치
2) App 토큰을 Actions에서 발급받아 Wiki에 push
3) 이 방식을 쓰려면 워크플로를 App 토큰 기반으로 변경해야 합니다.

## 워크플로 파일
- `.github/workflows/sync-wiki.yml`

## 검증 방법
1) `README.md` 또는 `SPEC.md`를 수정하고 `main`에 push
2) `NoA` 저장소 Actions 탭에서 “Sync docs to GitHub Wiki” 실행 확인
3) `NoA` 저장소의 Wiki 탭에서 `Home`, `SPEC`, `API`, `Docs` 페이지 갱신 확인

## 문제 해결
- `NOA_WIKI_TOKEN` 누락: Secrets에 등록했는지 확인
- 403/권한 오류: 봇 계정이 레포에 Write 권한을 가지는지, PAT에 `repo` 권한이 있는지 확인
- Wiki 비활성: 저장소 Settings에서 Wiki 기능이 켜져 있는지 확인
