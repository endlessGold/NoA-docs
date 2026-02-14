# GitBook 동기화 연결 (NoA-docs)

이 문서는 GitBook Space에 GitHub 레포지토리를 연결해 자동 동기화하는 방법을 정리합니다.

## 전제
- GitBook Space 초대 링크로 Space에 참여되어 있어야 합니다.
- GitBook은 GitHub 레포지토리를 “Source”로 연결해 변경을 자동 반영할 수 있습니다.
- NoA 프로젝트는 문서만 공개하기 위해 문서 전용 레포를 사용합니다.

## 동기화 소스(권장)
- GitHub 문서 레포: `https://github.com/endlessGold/NoA-docs`
- 브랜치: `main`

## GitBook에서 연결 절차
1) 초대 링크로 Space 참여
- 제공된 초대 링크로 들어가 Space에 Join 합니다.

2) GitHub 연동
- Space 설정 → Integrations(또는 Git Sync) → GitHub 연결
- GitHub OAuth 권한 요청이 뜨면 승인합니다.

3) Source로 레포 연결
- Source: GitHub Repository 선택
- Repository: `endlessGold/NoA-docs`
- Branch: `main`
- 동기화 방향: “GitHub → GitBook”을 기본으로 설정합니다.

4) 동기화 확인
- `endlessGold/NoA-docs`의 `README.md` 또는 `Docs/README.md`를 수정해 push
- GitBook에서 자동 반영되는지 확인합니다(필요 시 GitBook UI에서 수동 Sync 버튼 사용).

## OpenAPI(예시)로 동기화 확인
GitBook에서 OpenAPI 문서를 다루는 흐름을 빠르게 확인하려면, 아래 샘플 스펙 URL을 임시로 사용해 볼 수 있습니다.

- `https://gitbookio.github.io/onboarding-template-images/gitbook-petstore.yaml`

## 자주 발생하는 문제
- 레포가 목록에 안 보임: GitBook의 GitHub 연동 권한 범위에 `endlessGold` 계정이 포함되어 있는지 확인합니다.
- 동기화가 안 됨: GitBook이 바라보는 브랜치가 `main`인지 확인합니다.
- 페이지 구조가 원하는대로 안 나옴: GitBook은 폴더/파일 구조를 그대로 네비게이션으로 쓰지 않을 수 있습니다. 이 경우 `README`를 홈으로 두고 `Docs/README.md`를 “목차”로 유지하는 구성이 가장 안정적입니다.
