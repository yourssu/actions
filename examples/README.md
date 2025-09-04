# Example Templates

이 디렉토리에는 Yourssu Actions의 네이밍 컨벤션 검증을 사용하기 위한 예제 템플릿들이 포함되어 있습니다.

## 📁 구조

```
examples/
├── workflows/                 # GitHub Actions 워크플로우 예제
│   ├── basic-naming-validation.yml           # 기본 네이밍 검증
│   ├── custom-config-validation.yml          # 사용자 정의 설정 사용
│   └── flexible-validation.yml               # 고급 옵션 사용
└── configs/                   # 설정 파일 예제
    └── custom-naming-convention.yml          # 사용자 정의 네이밍 컨벤션
```

## 🚀 빠른 시작

### 1. 기본 사용법 (추천)

가장 간단한 방법으로 네이밍 컨벤션을 적용하려면:

1. `workflows/basic-naming-validation.yml`을 복사
2. 프로젝트의 `.github/workflows/` 디렉토리에 붙여넣기

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
```

### 2. 사용자 정의 설정 사용

프로젝트별로 다른 네이밍 컨벤션을 적용하려면:

1. `configs/custom-naming-convention.yml`을 복사
2. 프로젝트의 `.github/naming-convention.yml`로 저장
3. 필요에 따라 설정 수정
4. `workflows/custom-config-validation.yml`을 복사
5. 프로젝트의 `.github/workflows/` 디렉토리에 붙여넣기

### 3. 고급 옵션 사용

더 세밀한 제어가 필요하다면:

1. `workflows/flexible-validation.yml`을 복사
2. 프로젝트의 `.github/workflows/` 디렉토리에 붙여넣기
3. 필요에 따라 옵션 수정

## 🎛️ 주요 옵션들

### 기본 매개변수

| 매개변수 | 기본값 | 설명 |
|---------|--------|------|
| `team_config` | `yourssu-backend` | 사용할 팀 설정 |
| `config_file` | `.github/naming-convention.yml` | 사용자 정의 설정 파일 |
| `validate_drafts` | `false` | Draft PR 검증 여부 |
| `strict_mode` | `true` | 검증 실패 시 워크플로우 실패 여부 |

### 검증 활성화/비활성화

| 매개변수 | 기본값 | 설명 |
|---------|--------|------|
| `validate_issues` | `true` | 이슈 제목 검증 |
| `validate_prs` | `true` | PR 제목 검증 |
| `validate_branches` | `true` | 브랜치명 검증 |
| `validate_commits` | `true` | 커밋 메시지 검증 |

## 📋 워크플로우 유형별 특징

### Basic Naming Validation
- **특징**: 가장 간단한 설정
- **적용**: Yourssu Backend 팀 기본 규칙
- **권장**: 새로운 프로젝트나 표준 규칙을 따르는 프로젝트

### Custom Config Validation  
- **특징**: 프로젝트별 설정 파일 사용
- **적용**: 사용자 정의 규칙
- **권장**: 특별한 네이밍 규칙이 필요한 프로젝트

### Flexible Validation
- **특징**: 모든 옵션을 세밀하게 제어
- **적용**: 고급 옵션과 결과 처리
- **권장**: 복잡한 워크플로우나 단계적 적용이 필요한 프로젝트

## 🎯 사용 시나리오

### 신규 프로젝트
```yaml
uses: yourssu/actions/validation/naming-convention@v1
with:
  github_token: ${{ secrets.GITHUB_TOKEN }}
  team_config: "yourssu-backend"
  strict_mode: true
```

### 기존 프로젝트 (점진적 적용)
```yaml
uses: yourssu/actions/validation/naming-convention@v1
with:
  github_token: ${{ secrets.GITHUB_TOKEN }}
  team_config: "yourssu-backend"
  strict_mode: false  # 경고만 표시
```

### Draft PR 포함 검증
```yaml
uses: yourssu/actions/validation/naming-convention@v1
with:
  github_token: ${{ secrets.GITHUB_TOKEN }}
  validate_drafts: true
```

### 선택적 검증
```yaml
uses: yourssu/actions/validation/naming-convention@v1
with:
  github_token: ${{ secrets.GITHUB_TOKEN }}
  validate_issues: true
  validate_prs: true
  validate_branches: false  # 브랜치명은 검증하지 않음
  validate_commits: false   # 커밋은 검증하지 않음
```

### 사용자 정의 태그 추가
```yaml
# .github/naming-convention.yml에서 설정
tags:
  allowed:
    - feat
    - fix
    - refactor
    - docs  
    - chore
    - hotfix  # 추가 태그
    - test    # 추가 태그
```

## 🔧 문제 해결

### Q: Draft PR에서 브랜치명이 검증되지 않아요
A: 기본 동작입니다. `validate_drafts: true`로 설정하면 Draft PR도 검증됩니다.

### Q: 검증 실패해도 워크플로우가 실패하지 않길 원해요
A: `strict_mode: false`로 설정하면 검증 실패해도 댓글만 달리고 워크플로우는 성공합니다.

### Q: 특정 검증만 하고 싶어요
A: `validate_*` 매개변수로 원하는 검증만 활성화할 수 있습니다.

### Q: 커스텀 태그를 추가하고 싶어요
A: `configs/custom-naming-convention.yml`을 참고하여 설정 파일을 만들고, `tags.allowed`에 원하는 태그를 추가하세요.

## 📞 지원

- 질문이나 문제가 있다면 [Issues](https://github.com/yourssu/actions/issues)에 등록해 주세요.
- 개선 제안은 [Pull Request](https://github.com/yourssu/actions/pulls)로 보내주세요.