# Validation Actions

이슈, 풀 리퀘스트, 브랜치, 커밋 메시지의 네이밍 컨벤션을 검증하는 GitHub Actions 모음입니다.

## 사용 가능한 액션들

### 🎯 [Naming Convention](./naming-convention/) - **권장**

전체 워크플로우를 위한 완전한 네이밍 컨벤션 검증입니다.

**주요 기능:**
- 이슈, PR, 브랜치, 커밋을 자동으로 검증
- 설정 가능한 검증 모드 (엄격/경고)
- Draft PR 처리
- 자동 피드백 댓글
- 내장된 팀 설정

**빠른 시작:**
```yaml
- uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    team_config: "yourssu-backend"
```


## 어떤 액션을 사용해야 할까요?

### 대부분의 사용자: **Naming Convention Action**

```yaml
name: 네이밍 컨벤션 검증

on:
  issues:
    types: [opened, edited]
  pull_request:
    types: [opened, edited, ready_for_review, synchronize]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: 모든 네이밍 컨벤션 검증
        uses: yourssu/actions/validation/naming-convention@v1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
```


## 팀 설정

### Yourssu Backend 팀

모든 액션은 다음 규칙으로 `team_config: "yourssu-backend"`를 지원합니다:

**허용되는 태그:** `feat`, `fix`, `refactor`, `docs`, `chore`

**형식:**
- **이슈**: `feat: 사용자 인증 추가`
- **PR**: `[#123]; feat: 사용자 인증 추가`
- **브랜치**: `feat/123`
- **커밋**: `[#123]; feat: 로그인 엔드포인트 추가`

### 사용자 정의 설정

저장소에 `.github/naming-convention.yml` 파일을 생성하세요:

```yaml
tags:
  allowed:
    - feat
    - fix
    - refactor
    - docs
    - chore

patterns:
  issue_title: '^({tags}): .+'
  pr_title: '^\[#\d+\]; ({tags}): .+'
  branch_name: '^({tags})\/\d+$'
  commit_message: '^\[#\d+\]; ({tags}): .+'

messages:
  issue_error: "이슈 제목 형식이 올바르지 않습니다"
  pr_error: "PR 제목 형식이 올바르지 않습니다"
  branch_error: "브랜치명 형식이 올바르지 않습니다"
  commit_error: "커밋 메시지 형식이 올바르지 않습니다"

examples:
  issue_examples:
    - "feat: 새로운 기능 추가"
    - "fix: 버그 해결"
  pr_examples:
    - "[#123]; feat: 새로운 기능 추가"
    - "[#456]; fix: 버그 해결"
  branch_examples:
    - "feat/123"
    - "fix/456"
  commit_examples:
    - "[#123]; feat: 새로운 API 구현"
    - "[#456]; fix: 엣지 케이스 처리"
```


## 일반적인 사용 사례

### 완전한 프로젝트 검증
전체 자동화를 위해 **Naming Convention** 액션을 사용:

```yaml
- uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    team_config: "yourssu-backend"
```

### 점진적 도입
기존 워크플로우를 깨뜨리지 않기 위해 경고 모드로 시작:

```yaml
- uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    strict_mode: false  # 실패하지 않고 댓글만 생성
```

### 부분 검증
특정 검증만 활성화:

```yaml
- uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    validate_issues: true
    validate_prs: true
    validate_branches: false
    validate_commits: false
```


## 지원

질문, 버그 리포트, 기능 요청:
- [Issues](https://github.com/yourssu/actions/issues)
- [Pull Requests](https://github.com/yourssu/actions/pulls)

## 라이선스

MIT 라이선스