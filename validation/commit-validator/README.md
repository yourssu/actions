# Commit Validator Action

Pull Request의 커밋 메시지들을 네이밍 컨벤션에 따라 검증하는 GitHub Action입니다.

## 🎯 Features

- ✅ **PR 커밋 검증**: Pull Request의 모든 커밋 메시지 검증
- ✅ **Merge 커밋 스킵**: Merge 커밋은 자동으로 건너뜀
- ✅ **상세한 오류 정보**: 실패한 커밋의 SHA, 메시지, 작성자 정보 제공
- ✅ **팀 설정 지원**: 사전 정의된 팀 설정 사용 가능
- ✅ **사용자 정의 설정**: 프로젝트별 커스텀 규칙 지원
- ✅ **JSON 출력**: 구조화된 결과 데이터 제공

## 🚀 Usage

### 기본 사용법

```yaml
- name: Validate Commit Messages
  uses: yourssu/actions/validation/commit-validator@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    pr_number: ${{ github.event.pull_request.number }}
```

### 팀 설정 사용

```yaml
- name: Validate Commit Messages
  uses: yourssu/actions/validation/commit-validator@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    team_config: "yourssu-backend"
```

### 사용자 정의 설정

```yaml
- name: Validate Commit Messages
  uses: yourssu/actions/validation/commit-validator@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    config_file: ".github/custom-naming.yml"
```

## 📝 Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `github_token` | ❌ | `${{ github.token }}` | GitHub API 접근용 토큰 |
| `pr_number` | ❌ | `${{ github.event.number }}` | 검증할 PR 번호 |
| `team_config` | ❌ | `yourssu-backend` | 사전 정의된 팀 설정 |
| `config_file` | ❌ | `.github/naming-convention.yml` | 사용자 정의 설정 파일 경로 |

## 📤 Outputs

| Output | Description | Type |
|--------|-------------|------|
| `is_valid` | 모든 커밋이 유효한지 여부 | `string` (`true`/`false`) |
| `result_message` | 검증 결과 메시지 | `string` |
| `invalid_commits` | 유효하지 않은 커밋들의 배열 | `string` (JSON) |
| `total_commits` | 검증된 총 커밋 수 | `string` |

### invalid_commits JSON 구조

```json
[
  {
    "sha": "abc1234",
    "full_sha": "abc123456789...",
    "message": "add login feature",
    "author": "John Doe",
    "date": "2023-12-01T10:00:00Z"
  }
]
```

## 🎨 Yourssu Backend Team Rules

`team_config: "yourssu-backend"`를 사용하면 다음 커밋 메시지 규칙이 적용됩니다:

### 형식
- **패턴**: `[#이슈번호]; <태그>: <설명>`
- **태그**: `feat`, `fix`, `refactor`, `docs`, `chore`

### 예시

✅ **올바른 커밋 메시지:**
- `[#123]; feat: 사용자 로그인 기능 추가`
- `[#456]; fix: null pointer exception 해결`
- `[#789]; refactor: 데이터베이스 연결 로직 개선`

❌ **잘못된 커밋 메시지:**
- `add login feature`
- `feat: 로그인 추가`
- `#123 feat: 로그인 추가`
- `[123] feat: 로그인 추가`

## ⚙️ Advanced Usage

### 결과를 다른 단계에서 사용하기

```yaml
- name: Validate Commits
  uses: yourssu/actions/validation/commit-validator@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
  id: commit-validation

- name: Process Results
  if: steps.commit-validation.outputs.is_valid == 'false'
  run: |
    echo "Commit validation failed!"
    echo "Invalid commits: ${{ steps.commit-validation.outputs.invalid_commits }}"
    echo "Total commits: ${{ steps.commit-validation.outputs.total_commits }}"

- name: Create comment on PR
  if: steps.commit-validation.outputs.is_valid == 'false'
  uses: actions/github-script@v7
  with:
    script: |
      github.rest.issues.createComment({
        issue_number: context.issue.number,
        owner: context.repo.owner,
        repo: context.repo.repo,
        body: '${{ steps.commit-validation.outputs.result_message }}'
      })
```

### 조건부 실행

```yaml
- name: Validate Commits (Non-Draft PRs only)
  if: github.event_name == 'pull_request' && !github.event.pull_request.draft
  uses: yourssu/actions/validation/commit-validator@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
```

### JSON 결과 파싱

```yaml
- name: Parse Invalid Commits
  if: steps.commit-validation.outputs.is_valid == 'false'
  uses: actions/github-script@v7
  with:
    script: |
      const invalidCommits = JSON.parse('${{ steps.commit-validation.outputs.invalid_commits }}');
      
      console.log(`Found ${invalidCommits.length} invalid commits:`);
      
      invalidCommits.forEach(commit => {
        console.log(`- ${commit.sha}: "${commit.message}" by ${commit.author}`);
      });
```

## 🔄 Complete Workflow Example

```yaml
name: Validate PR Commits

on:
  pull_request:
    types: [ready_for_review, synchronize]

jobs:
  validate-commits:
    runs-on: ubuntu-latest
    if: github.event.pull_request.draft == false
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Validate Commit Messages
        uses: yourssu/actions/validation/commit-validator@v1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          team_config: "yourssu-backend"
        id: validation
        
      - name: Set status check
        uses: actions/github-script@v7
        with:
          script: |
            const isValid = '${{ steps.validation.outputs.is_valid }}' === 'true';
            const totalCommits = '${{ steps.validation.outputs.total_commits }}';
            
            await github.rest.repos.createCommitStatus({
              owner: context.repo.owner,
              repo: context.repo.repo,
              sha: context.payload.pull_request.head.sha,
              state: isValid ? 'success' : 'failure',
              target_url: `${context.payload.repository.html_url}/actions/runs/${context.runId}`,
              description: isValid ? `All ${totalCommits} commits valid` : 'Some commits failed validation',
              context: 'yourssu/commit-messages'
            });
        
      - name: Comment on PR if validation failed
        if: steps.validation.outputs.is_valid == 'false'
        uses: actions/github-script@v7
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: '${{ steps.validation.outputs.result_message }}'
            });
            
      - name: Fail if validation failed
        if: steps.validation.outputs.is_valid == 'false'
        run: exit 1
```

## 📊 Example Outputs

### 성공한 경우

**Console Output:**
```
✅ Valid commit: abc1234 - "[#123]; feat: 사용자 로그인 기능 추가"
✅ Valid commit: def5678 - "[#123]; feat: 로그인 폼 UI 구현"
⏭️  Skipping merge commit: merge123
🎉 All 2 commits are valid!
```

**result_message:**
```
✅ **모든 커밋 메시지가 네이밍 컨벤션을 따릅니다.**

검증된 커밋: 2개
```

### 실패한 경우

**Console Output:**
```
❌ Invalid commit: abc1234 - "add login feature"
✅ Valid commit: def5678 - "[#123]; feat: 로그인 폼 UI 구현"
❌ 1/2 commits failed validation
```

**result_message:**
```
❌ **다음 1개의 커밋 메시지가 네이밍 컨벤션을 따르지 않습니다:**

- `abc1234`: `add login feature`

**올바른 형식:** `^\[#\d+\]; (feat|fix|refactor|docs|chore): .+`
**사용 가능한 태그:** feat, fix, refactor, docs, chore

**올바른 예시:**
- `[#123]; feat: 사용자 인증 API 추가`
- `[#456]; fix: null pointer exception 해결`

📊 **검증 결과:** 1/2 통과
```

**invalid_commits JSON:**
```json
[
  {
    "sha": "abc1234",
    "full_sha": "abc123456789abcdef...",
    "message": "add login feature",
    "author": "John Doe",
    "date": "2023-12-01T10:00:00Z"
  }
]
```

## 🛠️ Troubleshooting

### Q: Merge 커밋도 검증하고 싶어요
A: 현재 Merge 커밋은 자동으로 스킵됩니다. 필요하다면 액션을 포크해서 수정하세요.

### Q: 특정 커밋만 제외하고 싶어요
A: 현재는 Merge 커밋만 제외됩니다. 다른 패턴 제외는 설정에서 지원하지 않습니다.

### Q: 커밋 메시지의 첫 줄만 검증하나요?
A: 네, 커밋 메시지의 첫 번째 줄만 검증합니다.

### Q: PR이 아닌 브랜치에서도 사용할 수 있나요?
A: 이 액션은 PR의 커밋을 검증하도록 설계되었습니다. 다른 용도로는 적합하지 않습니다.

## 🔧 Integration with Other Actions

### naming-convention Action과 함께 사용

이 액션은 `validation/naming-convention` 액션에서 내부적으로 사용됩니다:

```yaml
# naming-convention action 내부에서
- uses: ./validation/commit-validator
  with:
    github_token: ${{ inputs.github_token }}
    team_config: ${{ inputs.team_config }}
```

### 독립 사용 vs 통합 사용

- **독립 사용**: 커밋 메시지만 검증하고 싶을 때
- **통합 사용**: 이슈, PR, 브랜치명까지 모든 네이밍 검증을 원할 때는 `naming-convention` 액션 사용

## 📄 License

MIT License