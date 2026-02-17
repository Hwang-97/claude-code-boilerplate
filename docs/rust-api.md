# Rust (Axum) API 프로젝트 세팅

## 자동 세팅 (복사 후 실행)

```bash
# ============================================
# 프로젝트 초기화 스크립트
# 사용법: bash 에 복붙하세요
# ============================================

PROJECT_NAME="my-rust-api"

# 1. Cargo 프로젝트 생성
cargo init $PROJECT_NAME
cd $PROJECT_NAME

# 2. Cargo.toml 의존성 추가
cat > Cargo.toml << 'EOF'
[package]
name = "my-rust-api"
version = "0.1.0"
edition = "2021"

[dependencies]
axum = "0.8"
tokio = { version = "1", features = ["full"] }
serde = { version = "1", features = ["derive"] }
serde_json = "1"
tower = "0.5"
tower-http = { version = "0.6", features = ["cors", "trace"] }
tracing = "0.1"
tracing-subscriber = { version = "0.3", features = ["env-filter"] }
anyhow = "1"
dotenvy = "0.15"

[dev-dependencies]
axum-test = "16"
EOF

# 3. 디렉토리 구조
mkdir -p src/{handlers,services,models,middleware,config} tests .claude/commands

# 4. 메인 엔트리
cat > src/main.rs << 'EOF'
mod config;
mod handlers;

use axum::{Router, routing::get};
use std::net::SocketAddr;
use tracing_subscriber::EnvFilter;

#[tokio::main]
async fn main() -> anyhow::Result<()> {
    dotenvy::dotenv().ok();

    tracing_subscriber::fmt()
        .with_env_filter(EnvFilter::from_default_env())
        .init();

    let app = Router::new()
        .route("/health", get(handlers::health));

    let port: u16 = std::env::var("PORT")
        .unwrap_or_else(|_| "8080".to_string())
        .parse()?;
    let addr = SocketAddr::from(([0, 0, 0, 0], port));

    tracing::info!("Server starting on {}", addr);
    let listener = tokio::net::TcpListener::bind(addr).await?;
    axum::serve(listener, app).await?;

    Ok(())
}
EOF

# 5. 핸들러
cat > src/handlers/mod.rs << 'EOF'
use axum::Json;
use serde_json::{json, Value};

pub async fn health() -> Json<Value> {
    Json(json!({ "status": "healthy" }))
}
EOF

cat > src/config/mod.rs << 'EOF'
pub struct Config {
    pub port: u16,
}

impl Config {
    pub fn from_env() -> Self {
        Self {
            port: std::env::var("PORT")
                .unwrap_or_else(|_| "8080".to_string())
                .parse()
                .unwrap_or(8080),
        }
    }
}
EOF

# 6. .gitignore 추가
cat >> .gitignore << 'EOF'
.env
.env.local
.DS_Store
EOF

# 7. Claude Code 설정
cat > CLAUDE.md << 'EOF'
# Project Context

## 기술 스택
- Language: Rust (2021 edition)
- Framework: Axum 0.8
- Async: Tokio
- Serialization: serde + serde_json
- Error: anyhow (앱), thiserror (라이브러리)
- Logging: tracing

## 프로젝트 구조
src/
  ├── main.rs        → 엔트리, 라우터 구성
  ├── handlers/      → HTTP 핸들러 (라우트)
  ├── services/      → 비즈니스 로직
  ├── models/        → 데이터 모델
  ├── middleware/     → 미들웨어
  └── config/        → 설정 관리
tests/               → 통합 테스트

## 커맨드
- `cargo run` — 개발 실행
- `cargo build --release` — 릴리즈 빌드
- `cargo test` — 테스트
- `cargo clippy` — 린트
- `cargo fmt` — 포맷

## 코드 컨벤션
- snake_case (함수, 변수, 모듈)
- PascalCase (타입, 트레잇)
- SCREAMING_SNAKE_CASE (상수)
- unwrap() 금지 — ? 연산자 또는 expect() 사용
- clippy 경고 전부 해결
EOF

cat > .claude/settings.json << 'EOF'
{
  "permissions": {
    "allow": [
      "Bash(cargo run *)",
      "Bash(cargo build *)",
      "Bash(cargo test *)",
      "Bash(cargo clippy *)",
      "Bash(cargo fmt *)",
      "Bash(cargo add *)",
      "Bash(git *)"
    ]
  }
}
EOF

echo "✅ Rust + Axum 프로젝트 세팅 완료!"
echo "   cd $PROJECT_NAME && cargo run"
```
