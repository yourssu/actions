# Yourssu GitHub Actions

Yourssu에서 사용하는 재사용 가능한 GitHub Actions 모음입니다.

## 📁 프로젝트 구조

```
.
├── validation/                 # 네이밍 컨벤션 검증 액션들
│   ├── naming-convention/      # 완전한 네이밍 컨벤션 검증 (권장)
│   ├── commit-validator/       # PR 커밋 메시지 검증
│   └── validator/              # 내부용 단일 패턴 검증기
├── examples/                   # 사용 예시 및 템플릿
│   ├── workflows/              # 워크플로우 템플릿
│   └── configs/                # 설정 파일 예시
└── README.md                   # 이 파일
```

## 🚀 빠른 시작

### 기본 네이밍 컨벤션 검증

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
          team_config: "yourssu-backend"
```

## 📚 액션 디렉토리

각 액션의 자세한 사용법과 설정은 해당 디렉토리의 README를 참고하세요:

### [🔍 validation/](./validation/)

네이밍 컨벤션 검증을 위한 액션들을 제공합니다.

- **[naming-convention/](./validation/naming-convention/)** - 완전한 네이밍 컨벤션 검증 (대부분의 경우 권장)
- **[commit-validator/](./validation/commit-validator/)** - PR 커밋 메시지 전용 검증
- **[validator/](./validation/validator/)** - 내부용 단일 패턴 검증기

### [📋 examples/](./examples/)

워크플로우 템플릿과 설정 파일 예시를 제공합니다.

## ⚙️ 팀 설정

### Yourssu Backend 팀

`team_config: "yourssu-backend"`를 사용하면 다음 규칙이 적용됩니다:

**허용되는 태그:** `feat`, `fix`, `refactor`, `docs`, `chore`

**네이밍 패턴:**

- **이슈**: `feat: 사용자 인증 기능 추가`
- **PR**: `[#123]; feat: 사용자 인증 기능 추가`
- **브랜치**: `feat/123`
- **커밋**: `[#123]; feat: 로그인 엔드포인트 추가`

### 사용자 정의 설정

프로젝트 루트에 `.github/naming-convention.yml` 파일을 생성하여 커스텀 규칙을 설정할 수 있습니다.

자세한 설정 방법은 각 액션의 README를 참고하세요.

## 📖 사용 가이드

### 새 프로젝트

처음부터 완전한 검증을 적용:

```yaml
- uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    team_config: "yourssu-backend"
```

### 기존 프로젝트

단계적으로 적용:

```yaml
# 1단계: 경고 모드
- uses: yourssu/actions/validation/naming-convention@v1
  with:
    strict_mode: false

# 2단계: 선택적 검증
- uses: yourssu/actions/validation/naming-convention@v1
  with:
    validate_issues: true
    validate_prs: true
    validate_branches: false
    validate_commits: false

# 3단계: 완전한 검증
- uses: yourssu/actions/validation/naming-convention@v1
  with:
    strict_mode: true
```

## 🛠️ 문제 해결

**Q: 액션이 실행되지 않아요**  
A: 워크플로우에 `issues`와 `pull_request` 이벤트가 포함되었는지 확인하세요.

**Q: Draft PR이 검증되지 않아요**  
A: 기본 동작입니다. `validate_drafts: true`로 설정하면 Draft PR도 검증됩니다.

**Q: 기존 프로젝트에서 오류가 너무 많이 발생해요**  
A: `strict_mode: false`로 시작해서 점진적으로 적용하세요.

더 자세한 문제 해결 방법은 각 액션의 README를 참고하세요.

## 🤝 기여하기

기여를 환영합니다! 다음 방법으로 참여할 수 있습니다:

- 버그 리포트: [Issues](https://github.com/yourssu/actions/issues)
- 기능 제안: [Discussions](https://github.com/yourssu/actions/discussions)
- 코드 기여: [Pull Requests](https://github.com/yourssu/actions/pulls)
