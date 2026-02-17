# Go API 프로젝트 세팅

## 자동 세팅 (복사 후 실행)

```bash
# ============================================
# 프로젝트 초기화 스크립트
# 사용법: bash 에 복붙하세요
# ============================================

PROJECT_NAME="my-go-api"
MODULE_NAME="github.com/yourname/$PROJECT_NAME"  # ← 수정하세요

mkdir -p $PROJECT_NAME && cd $PROJECT_NAME

# 1. Go 모듈 초기화
go mod init $MODULE_NAME

# 2. 디렉토리 구조
mkdir -p cmd/server internal/{handler,service,repository,model,middleware,config} pkg tests .claude/commands

# 3. 메인 엔트리
cat > cmd/server/main.go << 'EOF'
package main

import (
	"log"
	"net/http"
	"os"

	"github.com/yourname/my-go-api/internal/handler"
)

func main() {
	port := os.Getenv("PORT")
	if port == "" {
		port = "8080"
	}

	mux := http.NewServeMux()
	mux.HandleFunc("GET /health", handler.Health)

	log.Printf("Server starting on :%s", port)
	if err := http.ListenAndServe(":"+port, mux); err != nil {
		log.Fatal(err)
	}
}
EOF

# 4. 핸들러 예시
cat > internal/handler/health.go << 'EOF'
package handler

import (
	"encoding/json"
	"net/http"
)

func Health(w http.ResponseWriter, r *http.Request) {
	w.Header().Set("Content-Type", "application/json")
	json.NewEncoder(w).Encode(map[string]string{
		"status": "healthy",
	})
}
EOF

# 5. 설정
cat > internal/config/config.go << 'EOF'
package config

import "os"

type Config struct {
	Port     string
	LogLevel string
}

func Load() *Config {
	return &Config{
		Port:     getEnv("PORT", "8080"),
		LogLevel: getEnv("LOG_LEVEL", "info"),
	}
}

func getEnv(key, fallback string) string {
	if v := os.Getenv(key); v != "" {
		return v
	}
	return fallback
}
EOF

# 6. .gitignore
cat > .gitignore << 'EOF'
bin/
tmp/
*.exe
.env
.env.local
.DS_Store
vendor/
EOF

# 7. Makefile
cat > Makefile << 'EOF'
.PHONY: run build test lint

run:
	go run cmd/server/main.go

build:
	go build -o bin/server cmd/server/main.go

test:
	go test ./... -v -race

lint:
	golangci-lint run ./...

tidy:
	go mod tidy
EOF

# 8. Claude Code 설정
cat > CLAUDE.md << 'EOF'
# Project Context

## 기술 스택
- Language: Go 1.22+
- HTTP: net/http (stdlib, Go 1.22 enhanced routing)
- Test: testing (stdlib)
- Lint: golangci-lint

## 프로젝트 구조 (Standard Go Layout)
cmd/
  └── server/main.go  → 엔트리 포인트
internal/              → 비공개 패키지
  ├── handler/         → HTTP 핸들러
  ├── service/         → 비즈니스 로직
  ├── repository/      → 데이터 액세스
  ├── model/           → 도메인 모델
  ├── middleware/       → HTTP 미들웨어
  └── config/          → 설정 관리
pkg/                   → 외부 공개 패키지
tests/                 → 통합 테스트

## 커맨드
- `make run` — 개발 서버 실행
- `make build` — 바이너리 빌드
- `make test` — 테스트 실행
- `make lint` — 린트

## 코드 컨벤션
- Effective Go 스타일 준수
- 에러는 즉시 처리 (if err != nil)
- 인터페이스는 사용처에서 정의
- context.Context는 첫 번째 인자
- 테스트 파일: *_test.go (같은 패키지)
EOF

cat > .claude/settings.json << 'EOF'
{
  "permissions": {
    "allow": [
      "Bash(go run *)",
      "Bash(go build *)",
      "Bash(go test *)",
      "Bash(go mod *)",
      "Bash(make *)",
      "Bash(golangci-lint *)",
      "Bash(git *)"
    ]
  }
}
EOF

# 9. 의존성 정리
go mod tidy

echo "✅ Go API 프로젝트 세팅 완료!"
echo "   cd $PROJECT_NAME && make run"
```

## Dockerfile (선택)

```dockerfile
FROM golang:1.22-alpine AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 go build -o /server cmd/server/main.go

FROM alpine:3.19
COPY --from=builder /server /server
EXPOSE 8080
CMD ["/server"]
```
