# Python + FastAPI 프로젝트 세팅

## 자동 세팅 (복사 후 실행)

```bash
# ============================================
# 프로젝트 초기화 스크립트
# 사용법: bash 에 복붙하세요
# ============================================

PROJECT_NAME="my-fastapi-project"
mkdir -p $PROJECT_NAME && cd $PROJECT_NAME

# 1. pyproject.toml
cat > pyproject.toml << 'EOF'
[project]
name = "my-fastapi-project"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = [
    "fastapi>=0.115.0",
    "uvicorn[standard]>=0.34.0",
    "pydantic>=2.0.0",
    "pydantic-settings>=2.0.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=8.0.0",
    "pytest-asyncio>=0.24.0",
    "httpx>=0.27.0",
    "ruff>=0.8.0",
    "mypy>=1.13.0",
]

[tool.ruff]
target-version = "py311"
line-length = 88

[tool.ruff.lint]
select = ["E", "F", "I", "N", "UP", "B", "SIM"]

[tool.pytest.ini_options]
asyncio_mode = "auto"
testpaths = ["tests"]

[tool.mypy]
python_version = "3.11"
strict = true
EOF

# 2. 디렉토리 구조
mkdir -p app/{routers,services,models,schemas} tests .claude/commands

# 3. 메인 앱
cat > app/__init__.py << 'EOF'
EOF

cat > app/main.py << 'EOF'
from fastapi import FastAPI

app = FastAPI(title="My API", version="0.1.0")


@app.get("/health")
async def health():
    return {"status": "healthy"}
EOF

cat > app/config.py << 'EOF'
from pydantic_settings import BaseSettings


class Settings(BaseSettings):
    app_name: str = "My API"
    debug: bool = False

    class Config:
        env_file = ".env"


settings = Settings()
EOF

# 4. 빈 __init__.py 파일들
touch app/routers/__init__.py
touch app/services/__init__.py
touch app/models/__init__.py
touch app/schemas/__init__.py
touch tests/__init__.py

# 5. 테스트 예시
cat > tests/test_health.py << 'EOF'
from httpx import AsyncClient, ASGITransport
from app.main import app


async def test_health():
    transport = ASGITransport(app=app)
    async with AsyncClient(transport=transport, base_url="http://test") as client:
        response = await client.get("/health")
    assert response.status_code == 200
    assert response.json()["status"] == "healthy"
EOF

# 6. .gitignore
cat > .gitignore << 'EOF'
__pycache__/
*.py[cod]
.env
.env.local
.venv/
dist/
*.egg-info/
.mypy_cache/
.pytest_cache/
.ruff_cache/
.DS_Store
EOF

# 7. .env.example
cat > .env.example << 'EOF'
DEBUG=false
EOF

# 8. Claude Code 설정
cat > CLAUDE.md << 'EOF'
# Project Context

## 기술 스택
- Language: Python 3.11+
- Framework: FastAPI
- Validation: Pydantic v2
- Test: pytest + pytest-asyncio
- Lint: ruff
- Type Check: mypy (strict)

## 프로젝트 구조
app/
  ├── main.py      → FastAPI 앱 진입점
  ├── config.py    → 설정 (pydantic-settings)
  ├── routers/     → API 라우터 (엔드포인트)
  ├── services/    → 비즈니스 로직
  ├── models/      → DB 모델
  └── schemas/     → Pydantic 스키마 (요청/응답)
tests/             → 테스트

## 커맨드
- `uvicorn app.main:app --reload` — 개발 서버
- `pytest` — 테스트 실행
- `ruff check .` — 린트
- `mypy app/` — 타입 체크

## 코드 컨벤션
- snake_case (변수, 함수, 파일명)
- PascalCase (클래스, Pydantic 모델)
- UPPER_SNAKE_CASE (상수)
- Type hints 필수
- docstring: Google 스타일
- Any 타입 금지
EOF

cat > .claude/settings.json << 'EOF'
{
  "permissions": {
    "allow": [
      "Bash(python *)",
      "Bash(uvicorn *)",
      "Bash(pytest *)",
      "Bash(ruff *)",
      "Bash(mypy *)",
      "Bash(pip install *)",
      "Bash(git *)"
    ]
  }
}
EOF

# 9. 가상환경 + 설치
python3 -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"

echo "✅ Python + FastAPI 프로젝트 세팅 완료!"
echo "   cd $PROJECT_NAME && source .venv/bin/activate"
echo "   uvicorn app.main:app --reload"
```

## Dockerfile (선택)

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY pyproject.toml ./
RUN pip install --no-cache-dir .
COPY app/ ./app/
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```
