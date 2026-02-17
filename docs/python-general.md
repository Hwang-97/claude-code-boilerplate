# Python 범용 프로젝트 세팅 (CLI / 라이브러리 / 스크립트)

## 자동 세팅 (복사 후 실행)

```bash
# ============================================
# 프로젝트 초기화 스크립트
# 사용법: bash 에 복붙하세요
# ============================================

PROJECT_NAME="my-python-project"
mkdir -p $PROJECT_NAME && cd $PROJECT_NAME

# 1. pyproject.toml
cat > pyproject.toml << 'EOF'
[project]
name = "my-python-project"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = []

[project.optional-dependencies]
dev = [
    "pytest>=8.0.0",
    "ruff>=0.8.0",
    "mypy>=1.13.0",
]

[project.scripts]
myapp = "src.main:main"

[tool.ruff]
target-version = "py311"
line-length = 88

[tool.ruff.lint]
select = ["E", "F", "I", "N", "UP", "B", "SIM"]

[tool.pytest.ini_options]
testpaths = ["tests"]

[tool.mypy]
python_version = "3.11"
strict = true
EOF

# 2. 디렉토리 구조
mkdir -p src tests .claude/commands

# 3. 엔트리
cat > src/__init__.py << 'EOF'
EOF

cat > src/main.py << 'EOF'
def main() -> None:
    print("Hello, World!")


if __name__ == "__main__":
    main()
EOF

# 4. 테스트
cat > tests/__init__.py << 'EOF'
EOF

cat > tests/test_main.py << 'EOF'
from src.main import main


def test_main(capsys):
    main()
    captured = capsys.readouterr()
    assert "Hello" in captured.out
EOF

# 5. .gitignore
cat > .gitignore << 'EOF'
__pycache__/
*.py[cod]
.env
.venv/
dist/
*.egg-info/
.mypy_cache/
.pytest_cache/
.ruff_cache/
.DS_Store
EOF

# 6. Claude Code 설정
cat > CLAUDE.md << 'EOF'
# Project Context

## 기술 스택
- Language: Python 3.11+
- Test: pytest
- Lint: ruff
- Type Check: mypy (strict)

## 프로젝트 구조
src/
  ├── __init__.py
  ├── main.py     → 엔트리 포인트
  └── ...
tests/
  └── test_*.py

## 커맨드
- `python -m src.main` — 실행
- `pytest` — 테스트
- `ruff check .` — 린트
- `ruff format .` — 포맷
- `mypy src/` — 타입 체크

## 코드 컨벤션
- PEP 8 준수 (ruff로 자동 적용)
- Type hints 필수
- docstring: Google 스타일
- Any 타입 금지
EOF

cat > .claude/settings.json << 'EOF'
{
  "permissions": {
    "allow": [
      "Bash(python *)",
      "Bash(pytest *)",
      "Bash(ruff *)",
      "Bash(mypy *)",
      "Bash(pip install *)",
      "Bash(git *)"
    ]
  }
}
EOF

# 7. 가상환경 + 설치
python3 -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"

echo "✅ Python 프로젝트 세팅 완료!"
echo "   cd $PROJECT_NAME && source .venv/bin/activate"
echo "   python -m src.main"
```
