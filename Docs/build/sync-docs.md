# 문서 자동 동기화 (NoA → NoA-docs)

이 문서는 비공개 저장소 `NoA`에서 문서만 추출해 공개 저장소 `NoA-docs`로 자동 동기화하는 방법을 설명합니다.

## 동작 방식
- 트리거: `NoA` 저장소의 `main` 브랜치에 push
- 동기화 대상: `README.md`, `SPEC.md`, `API.md`, `Docs/**`
- 결과: 공개 저장소 `endlessGold/NoA-docs`의 `main` 브랜치에 별도 커밋이 생성되고 자동 push 된다.

## 준비물
- 공개 문서 저장소: `endlessGold/NoA-docs` (Public)
- `NoA` 저장소에 GitHub Actions가 활성화되어 있어야 함

## Deploy Key 방식 (권장)
1) 공개 저장소 `NoA-docs`에서 Deploy key 생성/등록
- `NoA-docs` → Settings → Deploy keys → Add deploy key
- Key: 생성한 공개키(`id_ed25519.pub`) 내용 붙여넣기
- Allow write access: 체크

2) 비공개 저장소 `NoA`에 Secrets 등록
- `NoA` → Settings → Secrets and variables → Actions → New repository secret
- `NOA_DOCS_DEPLOY_KEY`: deploy key의 개인키(`id_ed25519`) 내용
- `NOA_DOCS_REPO_SSH`: `git@github.com:endlessGold/NoA-docs.git`

3) 워크플로 확인
- `.github/workflows/sync-docs.yml`이 `main` push 시 자동 실행됨
- Actions 탭에서 실행 로그 확인

## 문제 해결
- 권한 오류: Deploy key가 `NoA-docs`에 등록되었는지, “Allow write access”가 체크되었는지 확인
- Secret 오타: `NOA_DOCS_DEPLOY_KEY`, `NOA_DOCS_REPO_SSH` 이름 정확히 확인
- 동기화 안 됨: 워크플로 트리거 경로(README/SPEC/API/Docs)가 변경되었는지 확인
