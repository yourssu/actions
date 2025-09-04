# Yourssu GitHub Actions - Validation

이 저장소는 Yourssu Backend 팀의 네이밍 컨벤션 검증을 위한 GitHub Actions를 제공합니다.

## 📁 구조

```
.
└── validation/
    ├── naming-convention/   # 완전한 네이밍 컨벤션 검증
    ├── commit-validator/    # PR 커밋 메시지 검증
    └── validator/          # 내부용 단일 패턴 검증기
```

## 🚀 사용법

### 기본 사용법

```yaml
- name: Validate Naming Conventions
  uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    team_config: "yourssu-backend"
```

## 🎯 주요 기능

### 🔍 Naming Convention (`validation/naming-convention`)

Yourssu Backend 팀의 개발 워크플로우에 따른 완전한 네이밍 컨벤션 검증을 제공합니다.

#### 검증 대상
- **이슈 제목**: `<태그>: <설명>`
- **PR 제목**: `[#이슈번호]; <태그>: <설명>`  
- **브랜치명**: `<태그>/<이슈번호>`
- **커밋 메시지**: `[#이슈번호]; <태그>: <설명>`

#### 사용 가능한 태그
- `feat`: 새로운 기능 추가
- `fix`: 버그 수정
- `refactor`: 코드 리팩토링
- `docs`: 문서 작성/수정
- `chore`: 빌드/설정 관련

#### 입력 매개변수

| 매개변수 | 필수 | 기본값 | 설명 |
|---------|-----|--------|------|
| `github_token` | ❌ | `${{ github.token }}` | GitHub API 접근용 토큰 |
| `team_config` | ❌ | `yourssu-backend` | 팀 설정 이름 |
| `config_file` | ❌ | `.github/naming-convention.yml` | 사용자 정의 설정 파일 |
| `validate_drafts` | ❌ | `false` | Draft PR 검증 여부 |
| `strict_mode` | ❌ | `true` | 검증 실패 시 액션 실패 여부 |
| `validate_issues` | ❌ | `true` | 이슈 제목 검증 여부 |
| `validate_prs` | ❌ | `true` | PR 제목 검증 여부 |
| `validate_branches` | ❌ | `true` | 브랜치명 검증 여부 |
| `validate_commits` | ❌ | `true` | 커밋 메시지 검증 여부 |

#### 출력

| 출력 | 설명 |
|------|------|
| `validation_results` | 모든 검증 결과를 포함한 JSON 객체 |
| `all_valid` | 모든 검증이 통과했는지 여부 (`true`/`false`) |

#### 사용 예시

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
          team_config: "yourssu-backend"
          validate_drafts: false
          strict_mode: true
```

#### 고급 사용법

```yaml
- name: Flexible Naming Validation
  uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    config_file: ".github/custom-naming.yml"
    validate_drafts: true
    strict_mode: false
    validate_issues: true
    validate_prs: true
    validate_branches: false  # 브랜치명 검증 비활성화
    validate_commits: true
```

## ⚙️ 사용자 정의 설정

프로젝트별로 다른 규칙을 적용하려면 `.github/naming-convention.yml` 파일을 생성하세요:

```yaml
tags:
  allowed:
    - feat
    - fix
    - hotfix
    - refactor
    - docs
    - chore

patterns:
  issue_title: '^({tags}): .+'
  pr_title: '^\[#\d+\]; ({tags}): .+'
  branch_name: '^({tags})\/\d+$'
  commit_message: '^\[#\d+\]; ({tags}): .+'

messages:
  issue_error: "이슈 제목 형식이 올바르지 않습니다."
  pr_error: "PR 제목 형식이 올바르지 않습니다."
  branch_error: "브랜치명 형식이 올바르지 않습니다."
  commit_error: "커밋 메시지 형식이 올바르지 않습니다."

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

## 🔄 워크플로우 동작

### 검증 시점
- **이슈 제목**: 이슈 생성/수정 시
- **PR 제목**: PR 생성/수정 시
- **브랜치명 & 커밋 메시지**: PR이 Ready for Review 상태일 때 (Draft가 아닐 때)

### Draft PR 처리
- 기본적으로 Draft PR의 브랜치명과 커밋 메시지는 검증하지 않음
- `validate_drafts: true`로 설정하면 Draft PR도 검증

### 검증 실패 처리
- **strict_mode: true** (기본값): 검증 실패 시 액션 실패
- **strict_mode: false**: 검증 실패해도 액션은 성공, 댓글만 생성

## 🛠️ 내부 구조

### Naming Convention (`validation/naming-convention`)
- **메인 액션**: 모든 네이밍 컨벤션을 한 번에 검증
- **권장 사용법**: 대부분의 경우 이 액션을 사용

### Commit Validator (`validation/commit-validator`)
- **전용 액션**: PR 커밋 메시지만 검증
- **독립 사용**: 커밋 메시지만 검증하고 싶을 때
- **고급 기능**: 상세한 커밋 정보와 JSON 출력 제공

### Validator (`validation/validator`)
- **내부 액션**: 단일 문자열 패턴 검증
- **용도**: `naming-convention`과 `commit-validator`에서 내부적으로 사용
- **직접 사용**: 특별한 경우에만 권장

## 📞 지원

- 문의사항이나 버그 리포트: [Issues](https://github.com/yourssu/actions/issues)
- 개선 제안: [Pull Request](https://github.com/yourssu/actions/pulls)

## 📄 라이센스

이 프로젝트는 MIT 라이센스 하에 배포됩니다.