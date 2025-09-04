# Naming Convention Action

Yourssu Backend 팀의 네이밍 컨벤션을 완전히 검증하는 GitHub Action입니다. 이슈, PR, 브랜치, 커밋을 자동으로 검증하고 결과를 제공합니다.

## 🎯 Features

- ✅ **완전한 자동화**: 이슈, PR, 브랜치, 커밋을 한 번에 검증
- ✅ **유연한 설정**: 각 검증 대상을 개별적으로 활성화/비활성화 가능
- ✅ **Draft PR 지원**: Draft PR 검증 여부를 선택 가능
- ✅ **Strict/Warning 모드**: 검증 실패 시 액션 실패 여부 선택
- ✅ **자동 댓글**: 검증 실패 시 상세한 피드백 제공
- ✅ **상태 체크**: PR에 검증 상태를 표시
- ✅ **팀 설정**: 사전 정의된 팀 설정 사용 가능
- ✅ **사용자 정의**: 프로젝트별 커스텀 규칙 지원

## 🚀 Quick Start

### 기본 사용법

```yaml
name: Validate Naming Conventions

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
      
      - name: Validate Naming Conventions
        uses: yourssu/actions/validation/naming-convention@v1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
```

### 고급 설정

```yaml
- name: Custom Naming Validation
  uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    team_config: "yourssu-backend"
    validate_drafts: false
    strict_mode: true
    validate_issues: true
    validate_prs: true
    validate_branches: true
    validate_commits: true
```

## 📝 Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `github_token` | ❌ | `${{ github.token }}` | GitHub API 접근용 토큰 |
| `team_config` | ❌ | `yourssu-backend` | 사전 정의된 팀 설정 |
| `config_file` | ❌ | `.github/naming-convention.yml` | 사용자 정의 설정 파일 경로 |
| `validate_drafts` | ❌ | `false` | Draft PR의 브랜치/커밋 검증 여부 |
| `strict_mode` | ❌ | `true` | 검증 실패 시 액션 실패 여부 |
| `validate_issues` | ❌ | `true` | 이슈 제목 검증 활성화 |
| `validate_prs` | ❌ | `true` | PR 제목 검증 활성화 |
| `validate_branches` | ❌ | `true` | 브랜치명 검증 활성화 |
| `validate_commits` | ❌ | `true` | 커밋 메시지 검증 활성화 |

## 📤 Outputs

| Output | Description |
|--------|-------------|
| `validation_results` | 모든 검증 결과를 포함한 JSON 객체 |
| `all_valid` | 모든 활성화된 검증이 통과했는지 여부 (`true`/`false`) |

### validation_results JSON 구조

```json
{
  "issue": {
    "validated": true,
    "valid": true,
    "message": "✅ **네이밍 컨벤션 검증 통과**"
  },
  "pr": {
    "validated": true,
    "valid": false,
    "message": "❌ **PR 제목이 네이밍 컨벤션을 따르지 않습니다.**"
  },
  "branch": {
    "validated": false,
    "valid": null,
    "message": ""
  },
  "commits": {
    "validated": true,
    "valid": true,
    "message": "✅ **모든 커밋 메시지가 네이밍 컨벤션을 따릅니다.**"
  }
}
```

## 🎨 Yourssu Backend Team Rules

`team_config: "yourssu-backend"`를 사용하면 다음 규칙이 적용됩니다:

### 허용되는 태그
- `feat`: 새로운 기능 추가
- `fix`: 버그 수정
- `refactor`: 코드 리팩토링
- `docs`: 문서 작성/수정
- `chore`: 빌드/설정 관련

### 패턴 규칙

#### Issue Title
- **형식**: `<태그>: <설명>`
- **예시**: `feat: 사용자 로그인 기능 추가`

#### PR Title
- **형식**: `[#이슈번호]; <태그>: <설명>`
- **예시**: `[#123]; feat: 사용자 로그인 기능 추가`

#### Branch Name
- **형식**: `<태그>/<이슈번호>`
- **예시**: `feat/123`

#### Commit Message
- **형식**: `[#이슈번호]; <태그>: <설명>`
- **예시**: `[#123]; feat: 사용자 인증 API 추가`

## ⚙️ Custom Configuration

프로젝트별로 다른 규칙을 적용하려면 `.github/naming-convention.yml` 파일을 생성하고 `config_file` 매개변수에 경로를 지정하세요.

### 설정 파일 구조

```yaml
tags:
  allowed:
    - feat
    - fix
    - refactor
    - docs
    - chore
    - hotfix    # 추가 태그

patterns:
  issue_title: '^({tags}): .+'
  pr_title: '^\[#\d+\]; ({tags}): .+'
  branch_name: '^({tags})\/\d+$'
  commit_message: '^\[#\d+\]; ({tags}): .+'

messages:
  issue_error: |
    ❌ **이슈 제목이 네이밍 컨벤션을 따르지 않습니다.**
    
    올바른 형식: `<태그>: <설명>`
  pr_error: |
    ❌ **PR 제목이 네이밍 컨벤션을 따르지 않습니다.**
    
    올바른 형식: `[#이슈번호]; <태그>: <설명>`
  branch_error: |
    ❌ **브랜치명이 네이밍 컨벤션을 따르지 않습니다.**
    
    올바른 형식: `<태그>/<이슈번호>`
  commit_error: |
    ❌ **커밋 메시지가 네이밍 컨벤션을 따르지 않습니다.**
    
    올바른 형식: `[#이슈번호]; <태그>: <설명>`

examples:
  issue_examples:
    - "feat: 새로운 기능 추가"
    - "hotfix: 긴급 버그 수정"
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

## 🔄 Workflow Behavior

### 검증 시점

| 이벤트 | 검증 대상 |
|-------|----------|
| `issues: [opened, edited]` | 이슈 제목 |
| `pull_request: [opened, edited]` | PR 제목 |
| `pull_request: [ready_for_review, synchronize]` | 브랜치명, 커밋 메시지 (Draft가 아닐 때) |

### Draft PR 처리

- **기본값** (`validate_drafts: false`): Draft PR의 브랜치명과 커밋 메시지는 검증하지 않음
- **활성화** (`validate_drafts: true`): Draft PR도 모든 검증 수행

### 검증 실패 처리

- **Strict Mode** (`strict_mode: true`): 검증 실패 시 액션이 실패
- **Warning Mode** (`strict_mode: false`): 검증 실패해도 액션은 성공, 댓글만 생성

### 상태 체크

PR에서는 자동으로 상태 체크를 설정합니다:
- **컨텍스트**: `yourssu/naming-convention`
- **성공**: 모든 검증 통과
- **실패**: 하나 이상의 검증 실패

## 📋 Use Cases

### 1. 기본 팀 규칙 사용

```yaml
- uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    team_config: "yourssu-backend"
```

### 2. 커스텀 규칙 사용

```yaml
- uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    config_file: ".github/custom-naming.yml"
```

### 3. 특정 검증만 활성화

```yaml
- uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    validate_issues: true
    validate_prs: true
    validate_branches: false  # 브랜치명 검증 비활성화
    validate_commits: false   # 커밋 검증 비활성화
```

### 4. Warning 모드로 점진적 적용

```yaml
- uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    strict_mode: false  # 검증 실패해도 액션은 성공
```

### 5. Draft PR 포함 검증

```yaml
- uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    validate_drafts: true  # Draft PR도 검증
```

## 🛠️ Troubleshooting

### Q: 액션이 실행되지 않아요
A: 워크플로우 파일의 `on` 이벤트가 올바른지 확인하세요. `issues`와 `pull_request` 이벤트가 필요합니다.

### Q: Draft PR에서 브랜치명이 검증되지 않아요
A: 기본값입니다. `validate_drafts: true`로 설정하면 Draft PR도 검증됩니다.

### Q: 기존 프로젝트에 적용할 때 너무 많은 오류가 발생해요
A: `strict_mode: false`로 시작해서 점진적으로 규칙을 적용하세요.

### Q: 특정 패턴만 검증하고 싶어요
A: `validate_*` 매개변수로 원하는 검증만 활성화할 수 있습니다.

## 📊 Example Output

### 성공한 경우
```
✅ All naming convention validations passed!
```

### 실패한 경우 (댓글)
```markdown
## 🔍 네이밍 컨벤션 검증 결과

### PR 제목
❌ **PR 제목이 네이밍 컨벤션을 따르지 않습니다.**

PR 제목은 다음 형식을 따라야 합니다: `[#이슈번호]; <태그>: <설명>`

**현재 값:** `add login feature`
**예상 형식:** `^\[#\d+\]; (feat|fix|refactor|docs|chore): .+`

**올바른 예시:**
- `[#123]; feat: 사용자 로그인 기능 추가`
- `[#456]; fix: 메모리 누수 문제 해결`
```

## 📈 Advanced Usage

### 결과를 다른 단계에서 사용하기

```yaml
- name: Validate Naming Conventions
  uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
  id: validation

- name: Handle Results
  if: steps.validation.outputs.all_valid == 'false'
  run: |
    echo "Validation failed"
    echo "Results: ${{ steps.validation.outputs.validation_results }}"
```

### 조건부 실행

```yaml
- name: Validate Only Non-Draft PRs
  if: github.event_name == 'pull_request' && !github.event.pull_request.draft
  uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
```

## 📄 License

MIT License