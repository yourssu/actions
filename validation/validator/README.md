# Naming Convention Validator

네이밍 컨벤션을 검증하는 GitHub Action입니다. Yourssu Backend 팀의 개발 워크플로우에 따라 이슈 제목, PR 제목, 브랜치명, 커밋 메시지의 형식을 검증합니다.

## 🎯 Features

- ✅ 이슈 제목 검증
- ✅ PR 제목 검증  
- ✅ 브랜치명 검증
- ✅ 커밋 메시지 검증
- ✅ 팀별 사전 정의된 설정
- ✅ 사용자 정의 설정 지원
- ✅ 상세한 오류 메시지와 예시 제공

## 🚀 Usage

### Basic Usage

```yaml
- name: Validate Issue Title
  uses: yourssu/actions/validation/naming-convention@v1
  with:
    validation_string: ${{ github.event.issue.title }}
    pattern_name: "issue_title"
    team_config: "yourssu-backend"
```

### With Custom Config

```yaml
- name: Validate PR Title
  uses: yourssu/actions/validation/naming-convention@v1
  with:
    validation_string: ${{ github.event.pull_request.title }}
    pattern_name: "pr_title"
    config_file: ".github/my-naming-convention.yml"
```

## 📝 Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `validation_string` | ✅ | - | 검증할 문자열 |
| `pattern_name` | ✅ | - | 패턴 이름 (`issue_title`, `pr_title`, `branch_name`, `commit_message`) |
| `team_config` | ❌ | - | 사전 정의된 팀 설정 (`yourssu-backend`) |
| `config_file` | ❌ | `.github/naming-convention.yml` | 사용자 정의 설정 파일 경로 |

## 📤 Outputs

| Output | Description |
|--------|-------------|
| `is_valid` | 검증 통과 여부 (`true` 또는 `false`) |
| `result_message` | 검증 결과 메시지 (성공/실패 메시지와 예시 포함) |

## 🏷️ Pattern Names

| Pattern Name | Description | Format |
|--------------|-------------|--------|
| `issue_title` | 이슈 제목 | `<태그>: <설명>` |
| `pr_title` | PR 제목 | `[#이슈번호]; <태그>: <설명>` |
| `branch_name` | 브랜치명 | `<태그>/<이슈번호>` |
| `commit_message` | 커밋 메시지 | `[#이슈번호]; <태그>: <설명>` |

## 🎨 Yourssu Backend Team Config

`team_config: "yourssu-backend"`를 사용하면 다음 규칙이 적용됩니다:

### 허용되는 태그
- `feat`: 새로운 기능 추가
- `fix`: 버그 수정
- `refactor`: 코드 리팩토링
- `docs`: 문서 작성/수정
- `chore`: 빌드/설정 관련

### 패턴 예시

#### Issue Title
✅ **올바른 예시:**
- `feat: 사용자 로그인 기능 추가`
- `fix: 메모리 누수 문제 해결`
- `docs: API 문서 업데이트`

❌ **잘못된 예시:**
- `add login feature`
- `Feature: 로그인 추가`
- `feat:로그인 기능`

#### PR Title
✅ **올바른 예시:**
- `[#123]; feat: 사용자 로그인 기능 추가`
- `[#456]; fix: 메모리 누수 문제 해결`

❌ **잘못된 예시:**
- `feat: 사용자 로그인 기능 추가`
- `[123]; feat: 로그인 추가`
- `#123; feat: 로그인 추가`

#### Branch Name
✅ **올바른 예시:**
- `feat/123`
- `fix/456`
- `chore/789`

❌ **잘못된 예시:**
- `feature/123`
- `feat-123`
- `feat/add-login`

#### Commit Message
✅ **올바른 예시:**
- `[#123]; feat: 사용자 인증 API 추가`
- `[#123]; feat: 로그인 폼 UI 구현`
- `[#456]; fix: null pointer exception 해결`

❌ **잘못된 예시:**
- `feat: 로그인 추가`
- `#123 feat: 로그인 추가`
- `[123] feat: 로그인 추가`

## ⚙️ Custom Configuration

프로젝트별로 다른 규칙을 적용하려면 설정 파일을 생성할 수 있습니다.

### 설정 파일 예시 (`.github/naming-convention.yml`)

```yaml
tags:
  allowed:
    - feat
    - fix
    - refactor
    - docs
    - chore
    - hotfix

patterns:
  issue_title: '^(feat|fix|refactor|docs|chore|hotfix): .+'
  pr_title: '^\[#\d+\]; (feat|fix|refactor|docs|chore|hotfix): .+'
  branch_name: '^(feat|fix|refactor|docs|chore|hotfix)\/\d+$'
  commit_message: '^\[#\d+\]; (feat|fix|refactor|docs|chore|hotfix): .+'

messages:
  issue_error: |
    ❌ **이슈 제목이 네이밍 컨벤션을 따르지 않습니다.**
    
    이슈 제목은 다음 형식을 따라야 합니다: `<태그>: <설명>`
  pr_error: |
    ❌ **PR 제목이 네이밍 컨벤션을 따르지 않습니다.**
    
    PR 제목은 다음 형식을 따라야 합니다: `[#이슈번호]; <태그>: <설명>`
  branch_error: |
    ❌ **브랜치 이름이 네이밍 컨벤션을 따르지 않습니다.**
    
    브랜치 이름은 다음 형식을 따라야 합니다: `<태그>/<이슈번호>`
  commit_error: |
    ❌ **커밋 메시지가 네이밍 컨벤션을 따르지 않습니다.**
    
    커밋 메시지는 다음 형식을 따라야 합니다: `[#이슈번호]; <태그>: <설명>`

examples:
  issue_examples:
    - "feat: 새로운 기능 추가"
    - "fix: 버그 수정"
    - "hotfix: 긴급 수정"
  pr_examples:
    - "[#123]; feat: 새로운 기능 추가"
    - "[#456]; hotfix: 긴급 버그 수정"
  branch_examples:
    - "feat/123"
    - "hotfix/456"
  commit_examples:
    - "[#123]; feat: 새로운 API 추가"
    - "[#456]; hotfix: 크리티컬 버그 수정"
```

## 🔄 Complete Workflow Examples

### Issue and PR Title Validation

```yaml
name: Validate Titles

on:
  issues:
    types: [opened, edited]
  pull_request:
    types: [opened, edited]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        
      - name: Validate Issue Title
        if: github.event_name == 'issues'
        uses: yourssu/actions/validation/naming-convention@v1
        with:
          validation_string: ${{ github.event.issue.title }}
          pattern_name: 'issue_title'
          team_config: 'yourssu-backend'
        id: validate-issue
        
      - name: Validate PR Title
        if: github.event_name == 'pull_request'
        uses: yourssu/actions/validation/naming-convention@v1
        with:
          validation_string: ${{ github.event.pull_request.title }}
          pattern_name: 'pr_title'
          team_config: 'yourssu-backend'
        id: validate-pr
        
      - name: Comment if validation fails
        if: (github.event_name == 'issues' && steps.validate-issue.outputs.is_valid == 'false') || (github.event_name == 'pull_request' && steps.validate-pr.outputs.is_valid == 'false')
        uses: actions/github-script@v7
        with:
          script: |
            const message = '${{ steps.validate-issue.outputs.result_message || steps.validate-pr.outputs.result_message }}';
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: message
            });
```

### Branch and Commit Validation for Ready PRs

```yaml
name: Validate Branch and Commits

on:
  pull_request:
    types: [ready_for_review, synchronize]

jobs:
  validate:
    runs-on: ubuntu-latest
    if: github.event.pull_request.draft == false
    
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
          
      - name: Validate Branch Name
        uses: yourssu/actions/validation/naming-convention@v1
        with:
          validation_string: ${{ github.event.pull_request.head.ref }}
          pattern_name: 'branch_name'
          team_config: 'yourssu-backend'
        id: validate-branch
        
      - name: Get commits and validate
        uses: actions/github-script@v7
        id: validate-commits
        with:
          script: |
            const commits = await github.rest.pulls.listCommits({
              owner: context.repo.owner,
              repo: context.repo.repo,
              pull_number: context.issue.number
            });
            
            // Validate each commit message
            // (Implementation details...)
```

## 🛠️ Development

액션을 수정하거나 새로운 팀 설정을 추가하려면:

1. `configs/` 디렉토리에 새로운 YAML 파일 생성
2. `action.yml`의 스크립트 부분에서 새 설정 참조
3. 테스트 후 새 버전 태그 생성

## 📋 Changelog

- **v1.0.0**: 초기 릴리스
  - 기본 네이밍 컨벤션 검증 기능
  - Yourssu Backend 팀 설정 지원
  - 사용자 정의 설정 지원