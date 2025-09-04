# Validation Actions

네이밍 컨벤션 검증을 위한 GitHub Actions입니다.

## 📁 Available Actions

### 🔍 Naming Convention (`naming-convention/`)

완전한 네이밍 컨벤션 검증을 제공하는 메인 액션입니다. 이슈, PR, 브랜치, 커밋을 한 번에 검증할 수 있습니다.

**추천 사용법:**
```yaml
- uses: yourssu/actions/validation/naming-convention@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    team_config: "yourssu-backend"
```

### 📝 Commit Validator (`commit-validator/`)

Pull Request의 커밋 메시지들을 검증하는 전용 액션입니다. 독립적으로 사용하거나 커스텀 워크플로우에서 활용할 수 있습니다.

**독립 사용법:**
```yaml
- uses: yourssu/actions/validation/commit-validator@v1
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    pr_number: ${{ github.event.pull_request.number }}
    team_config: "yourssu-backend"
```

### 🔧 Validator (`validator/`)

단일 문자열에 대한 패턴 검증을 수행하는 내부용 액션입니다. `naming-convention` 액션에서 내부적으로 사용됩니다.

**내부 사용 예시:**
```yaml
- uses: yourssu/actions/validation/validator@v1
  with:
    validation_string: "feat: 새로운 기능"
    pattern_name: "issue_title"
    team_config: "yourssu-backend"
```

## 🎯 권장사항

대부분의 경우 `naming-convention` 액션을 사용하는 것을 권장합니다. 이 액션은 전체 워크플로우를 자동화하고 더 나은 사용자 경험을 제공합니다.

`validator`는 특별한 커스텀 워크플로우가 필요한 경우에만 직접 사용하세요.