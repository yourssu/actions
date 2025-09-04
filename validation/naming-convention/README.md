# Naming Convention Action

팀 표준에 따라 이슈, 풀 리퀘스트, 브랜치, 커밋 메시지의 네이밍 컨벤션을 검증하는 종합적인 GitHub Action입니다.

## 주요 기능

- **완전한 검증**: 이슈, PR, 브랜치, 커밋을 자동으로 검증
- **유연한 설정**: 특정 검증 유형을 활성화/비활성화 가능
- **Draft PR 지원**: Draft PR 검증 여부를 선택 가능
- **엄격/경고 모드**: 액션 실패 동작을 제어
- **자동 댓글**: 검증 실패 시 상세한 피드백 제공
- **상태 체크**: PR 검증 상태를 표시
- **팀 사전 설정**: 내장된 팀별 설정 사용 가능
- **사용자 정의 규칙**: 프로젝트별 사용자 정의 설정 지원

## 빠른 시작

### 기본 사용법

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

      - name: 네이밍 컨벤션 검증
        uses: yourssu/actions/validation/naming-convention@v1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
```

## 설정

### 입력값

| 입력값              | 필수   | 기본값                          | 설명                        |
| ------------------- | ------ | ------------------------------- | --------------------------- |
| `github_token`      | 아니오 | `${{ github.token }}`           | GitHub API 접근 토큰        |
| `team_config`       | 아니오 | `yourssu-backend`               | 사전 정의된 팀 설정         |
| `config_file`       | 아니오 | `.github/naming-convention.yml` | 사용자 정의 설정 파일 경로  |
| `validate_drafts`   | 아니오 | `false`                         | Draft PR 검증 여부          |
| `strict_mode`       | 아니오 | `true`                          | 검증 오류 시 액션 실패 여부 |
| `validate_issues`   | 아니오 | `true`                          | 이슈 제목 검증 활성화       |
| `validate_prs`      | 아니오 | `true`                          | PR 제목 검증 활성화         |
| `validate_branches` | 아니오 | `true`                          | 브랜치명 검증 활성화        |
| `validate_commits`  | 아니오 | `true`                          | 커밋 메시지 검증 활성화     |

### 출력값

| 출력값               | 설명                                                  |
| -------------------- | ----------------------------------------------------- |
| `validation_results` | 모든 검증 결과를 포함한 JSON 객체                     |
| `all_valid`          | 모든 활성화된 검증이 통과했는지 여부 (`true`/`false`) |

## 팀 설정

### Yourssu Backend 팀 규칙

`team_config: "yourssu-backend"`를 사용하면 다음 규칙이 적용됩니다:

**허용되는 태그:**

- `feat`: 새로운 기능
- `fix`: 버그 수정
- `refactor`: 코드 리팩토링
- `docs`: 문서화
- `chore`: 빌드/설정 변경

**패턴:**

| 유형        | 형식                          | 예시                                   |
| ----------- | ----------------------------- | -------------------------------------- |
| 이슈 제목   | `<태그>: <설명>`              | `feat: 사용자 인증 추가`               |
| PR 제목     | `[#이슈번호]; <태그>: <설명>` | `[#123]; feat: 사용자 인증 추가`       |
| 브랜치명    | `<태그>/<이슈번호>`           | `feat/123`                             |
| 커밋 메시지 | `[#이슈번호]; <태그>: <설명>` | `[#123]; feat: 로그인 엔드포인트 추가` |

## 고급 사용법

### 사용자 정의 팀 설정

`.github/naming-convention.yml` 파일을 생성하세요:

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
  issue_title: "^({tags}): .+"
  pr_title: '^\[#\d+\]; ({tags}): .+'
  branch_name: '^({tags})\/\d+$'
  commit_message: '^\[#\d+\]; ({tags}): .+'

messages:
  issue_error: |
    ❌ **이슈 제목이 네이밍 컨벤션을 따르지 않습니다.**

    형식: `<태그>: <설명>`
  pr_error: |
    ❌ **PR 제목이 네이밍 컨벤션을 따르지 않습니다.**

    형식: `[#이슈번호]; <태그>: <설명>`
  branch_error: |
    ❌ **브랜치명이 네이밍 컨벤션을 따르지 않습니다.**

    형식: `<태그>/<이슈번호>`
  commit_error: |
    ❌ **커밋 메시지가 네이밍 컨벤션을 따르지 않습니다.**

    형식: `[#이슈번호]; <태그>: <설명>`

examples:
  issue_examples:
    - "feat: 새로운 기능 추가"
    - "hotfix: 긴급 수정"
  pr_examples:
    - "[#123]; feat: 새로운 기능 추가"
    - "[#456]; hotfix: 긴급 수정"
  branch_examples:
    - "feat/123"
    - "hotfix/456"
  commit_examples:
    - "[#123]; feat: 새로운 API 엔드포인트 추가"
    - "[#456]; hotfix: 치명적 버그 수정"
```

### 선택적 검증

```yaml
- name: 제목만 검증
  uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    validate_issues: true
    validate_prs: true
    validate_branches: false
    validate_commits: false
```

### 경고 모드

```yaml
- name: 점진적 도입
  uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    strict_mode: false # 액션 실패 없이 댓글만 추가
```

### Draft PR 포함

```yaml
- name: 모든 것 검증
  uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    validate_drafts: true
```

## 동작 세부사항

### 검증 트리거

| 이벤트                                          | 검증 대상                               |
| ----------------------------------------------- | --------------------------------------- |
| `issues: [opened, edited]`                      | 이슈 제목                               |
| `pull_request: [opened, edited]`                | PR 제목                                 |
| `pull_request: [ready_for_review, synchronize]` | 브랜치명, 커밋 메시지 (Draft가 아닌 PR) |

### Draft PR 처리

- **기본값** (`validate_drafts: false`): Draft PR은 브랜치/커밋 검증 건너뛰기
- **활성화** (`validate_drafts: true`): Draft PR도 모든 검증 수행

### 실패 모드

- **엄격 모드** (`strict_mode: true`): 검증 오류 시 액션 실패
- **경고 모드** (`strict_mode: false`): 검증 오류 시에도 액션 성공, 댓글만 생성

### 상태 체크

풀 리퀘스트에 대해 액션은 다음을 설정합니다:

- **컨텍스트**: `yourssu/naming-convention`
- **성공**: 모든 검증 통과
- **실패**: 하나 이상의 검증 실패

## 출력 예시

### 성공

```
✅ 모든 네이밍 컨벤션 검증이 통과했습니다!
```

### 실패 댓글

```markdown
## 🔍 네이밍 컨벤션 검증 결과

### PR 제목 ❌

❌ **PR 제목이 네이밍 컨벤션을 따르지 않습니다.**

형식: `[#이슈번호]; <태그>: <설명>`

**현재값:** `add login feature`
**예상형식:** `^\[#\d+\]; (feat|fix|refactor|docs|chore): .+`

**올바른 예시:**

- `[#123]; feat: 사용자 인증 추가`
- `[#456]; fix: 메모리 누수 수정`
```

## 통합 예시

### 출력값 사용

```yaml
- name: 네이밍 검증
  uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
  id: validation

- name: 결과 처리
  if: steps.validation.outputs.all_valid == 'false'
  run: |
    echo "검증 실패"
    echo "결과: ${{ steps.validation.outputs.validation_results }}"
```

### 조건부 실행

```yaml
- name: Draft가 아닌 PR만 검증
  if: github.event_name == 'pull_request' && !github.event.pull_request.draft
  uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
```

## 문제 해결

**Q: 액션이 실행되지 않습니다**  
A: 워크플로우에 올바른 `on` 이벤트가 포함되어 있는지 확인하세요: `issues`와 `pull_request`.

**Q: Draft PR이 검증되지 않습니다**  
A: 기본 동작입니다. Draft를 검증하려면 `validate_drafts: true`로 설정하세요.

**Q: 기존 프로젝트에서 검증 오류가 너무 많습니다**  
A: `strict_mode: false`로 시작해서 점진적으로 규칙을 적용하세요.

**Q: 특정 패턴만 검증하고 싶습니다**  
A: `validate_*` 매개변수를 사용해서 원하는 검증만 활성화할 수 있습니다.

## 예제

다양한 사용 시나리오에 대한 예제들을 [`examples/`](./examples/) 디렉토리에서 확인할 수 있습니다:

### 워크플로우 예제

- **[기본 사용법](./examples/workflows/basic-usage.yml)** - 가장 간단한 설정
- **[고급 사용법](./examples/workflows/advanced-usage.yml)** - 모든 옵션을 활용한 고급 설정
- **[점진적 도입](./examples/workflows/gradual-implementation.yml)** - 기존 프로젝트에 점진적 적용
- **[사용자 정의 설정](./examples/workflows/custom-config.yml)** - 커스텀 규칙 사용

### 설정 파일 예제

- **[사용자 정의 설정 파일](./examples/configs/custom-naming-convention.yml)** - 프로젝트별 커스텀 규칙

## 라이선스

MIT 라이선스
