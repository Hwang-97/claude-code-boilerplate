# Claude Code 보일러플레이트

Claude Code와 함께 새 프로젝트를 시작할 때 사용하는 템플릿 모음입니다.

---

## 이게 뭔가요?

Claude Code는 프로젝트 루트의 `CLAUDE.md`와 `.claude/` 폴더를 읽어서
프로젝트 컨텍스트, 코드 컨벤션, 슬래시 커맨드를 인식합니다.
이 보일러플레이트는 그 세팅을 빠르게 해줍니다.

## 어떻게 사용하나요?

### 방법 1: 원클릭 세팅 (추천)

`docs/` 폴더에서 사용할 언어/프레임워크 가이드를 열고,
"자동 세팅" 섹션의 bash 스크립트를 통째로 복사해서 터미널에 붙여넣기하면 됩니다.

```bash
# 예시: Node.js + TypeScript 프로젝트
# docs/node-typescript.md 의 스크립트를 복사해서 실행
```

프로젝트 구조, 설정 파일, CLAUDE.md, .claude/ 전부 자동 생성됩니다.

### 방법 2: 수동 세팅

1. 새 프로젝트 폴더를 만듭니다
2. 이 보일러플레이트에서 필요한 파일을 복사합니다:
   - `CLAUDE.md` → 프로젝트 루트 (내용 수정 필요)
   - `.claude/settings.json` → 권한 설정
   - `.claude/commands/*.md` → 슬래시 커맨드
3. CLAUDE.md를 프로젝트에 맞게 수정합니다

## 지원하는 언어/프레임워크

| 가이드 | 파일 | 포함 내용 |
|--------|------|-----------|
| Node.js + TypeScript | [docs/node-typescript.md](docs/node-typescript.md) | ESM, Vitest, ESLint |
| Python + FastAPI | [docs/python-fastapi.md](docs/python-fastapi.md) | Pydantic v2, pytest, ruff, mypy |
| Python 범용 | [docs/python-general.md](docs/python-general.md) | CLI/라이브러리/스크립트 |
| Next.js (React) | [docs/nextjs.md](docs/nextjs.md) | App Router, Tailwind, Vitest |
| Go API | [docs/go-api.md](docs/go-api.md) | stdlib net/http, Makefile |
| Rust (Axum) | [docs/rust-api.md](docs/rust-api.md) | Axum, Tokio, tracing |
| Spring Boot | [docs/spring-boot.md](docs/spring-boot.md) | Java/Kotlin, Gradle |
| Flutter | [docs/flutter.md](docs/flutter.md) | Riverpod, go_router |

## 각 가이드에 포함된 것

- **자동 세팅 스크립트** — bash에 복붙하면 프로젝트 구조 자동 생성
- **CLAUDE.md 템플릿** — 해당 스택에 맞는 컨텍스트/컨벤션
- **.claude/settings.json** — 허용 명령어 목록
- **디렉토리 구조** — 해당 생태계의 관례를 따르는 폴더 구조
- **기본 코드** — 헬스체크 등 최소 동작 코드

## 슬래시 커맨드

`.claude/commands/` 폴더의 `.md` 파일이 슬래시 커맨드로 등록됩니다.

| 커맨드 | 설명 |
|--------|------|
| `/plan [기능]` | 구현 계획 수립 — 영향 범위, 단계별 계획, 리스크 분석 |
| `/review` | 코드 리뷰 — git diff 분석, 심각도별 이슈 리포트 |
| `/test [파일]` | 테스트 작성 — AAA 패턴, 엣지케이스 포함 |

추가 커맨드는 [docs/commands-reference.md](docs/commands-reference.md)를 참고하세요.
`/refactor`, `/doc`, `/debug` 등의 예시가 있습니다.

## 핵심 파일 설명

```
CLAUDE.md                    ← Claude가 읽는 프로젝트 컨텍스트
.claude/
  ├── settings.json          ← 권한 설정 (허용/차단 명령어)
  └── commands/
      ├── plan.md            ← /plan 커맨드
      ├── review.md          ← /review 커맨드
      └── test.md            ← /test 커맨드
```

### CLAUDE.md 작성 팁

Claude가 이 파일을 읽고 프로젝트를 이해하므로, 다음 내용을 포함하면 좋습니다:

1. **기술 스택** — 언어, 프레임워크, 주요 라이브러리
2. **프로젝트 구조** — 디렉토리별 역할
3. **커맨드** — 빌드, 테스트, 린트 등 자주 쓰는 명령어
4. **코드 컨벤션** — 네이밍, 파일 구조, 에러 처리 방침
5. **금지 사항** — Claude가 하지 말아야 할 것

### settings.json 작성 팁

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run *)",     // npm 스크립트 허용
      "Bash(git *)"          // git 명령 허용
    ],
    "deny": [
      "Bash(rm -rf /)",      // 위험한 명령 차단
      "Bash(sudo *)"
    ]
  }
}
```

## 사용 예시

### 1. 새 Python API 프로젝트 시작

```bash
# docs/python-fastapi.md 의 스크립트 실행 후...

cd my-fastapi-project
source .venv/bin/activate
claude  # Claude Code 실행

# Claude Code 안에서:
# > /plan 사용자 인증 기능 구현해줘
# > /test app/routers/auth.py
# > /review
```

### 2. 기존 프로젝트에 Claude Code 적용

```bash
cd my-existing-project

# CLAUDE.md 생성 (프로젝트에 맞게 수정)
cp /path/to/boilerplate/CLAUDE.md .

# 슬래시 커맨드 복사
cp -r /path/to/boilerplate/.claude .

# CLAUDE.md 수정
# 기술 스택, 구조, 커맨드 등을 프로젝트에 맞게 편집

claude  # Claude Code 실행
```

### 3. 커스텀 슬래시 커맨드 추가

```bash
# .claude/commands/deploy.md 생성
cat > .claude/commands/deploy.md << 'EOF'
배포 절차를 실행해주세요.

1. 테스트 실행
2. 빌드
3. Docker 이미지 빌드
4. 결과 보고

$ARGUMENTS
EOF

# Claude Code에서 /deploy 로 사용 가능
```
