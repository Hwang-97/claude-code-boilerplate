# Node.js + TypeScript 프로젝트 세팅

## 자동 세팅 (복사 후 실행)

```bash
# ============================================
# 프로젝트 초기화 스크립트
# 사용법: bash 에 복붙하세요
# ============================================

PROJECT_NAME="my-node-project"
mkdir -p $PROJECT_NAME && cd $PROJECT_NAME

# 1. package.json 생성
cat > package.json << 'EOF'
{
  "name": "my-node-project",
  "version": "0.1.0",
  "type": "module",
  "scripts": {
    "dev": "tsx watch src/index.ts",
    "build": "tsc",
    "start": "node dist/index.js",
    "test": "vitest run",
    "test:watch": "vitest",
    "lint": "eslint src/",
    "typecheck": "tsc --noEmit"
  },
  "engines": { "node": ">=20.0.0" }
}
EOF

# 2. tsconfig.json
cat > tsconfig.json << 'EOF'
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "lib": ["ES2022"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "declaration": true,
    "sourceMap": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
EOF

# 3. 디렉토리 구조
mkdir -p src tests .claude/commands

# 4. 엔트리 파일
cat > src/index.ts << 'EOF'
console.log("Hello from TypeScript!");
EOF

# 5. .gitignore
cat > .gitignore << 'EOF'
node_modules/
dist/
.env
.env.local
*.log
.DS_Store
coverage/
EOF

# 6. Claude Code 설정
cat > CLAUDE.md << 'EOF'
# Project Context

## 기술 스택
- Language: TypeScript (strict mode)
- Runtime: Node.js 20+
- Test: Vitest
- Package Manager: npm

## 컨벤션
- ESM (import/export) 사용
- 변수/함수: camelCase, 타입: PascalCase, 상수: UPPER_SNAKE_CASE
- 파일명: kebab-case.ts
- any 타입 금지
EOF

cat > .claude/settings.json << 'EOF'
{
  "permissions": {
    "allow": [
      "Bash(npm run *)",
      "Bash(npx tsc *)",
      "Bash(npx vitest *)",
      "Bash(node *)",
      "Bash(git *)"
    ]
  }
}
EOF

# 7. 의존성 설치
npm install
npm install -D typescript tsx vitest @types/node eslint

echo "✅ Node.js + TypeScript 프로젝트 세팅 완료!"
echo "   cd $PROJECT_NAME && npm run dev"
```

## CLAUDE.md 템플릿

```markdown
# Project Context

## 기술 스택
- Language: TypeScript (strict mode)
- Runtime: Node.js 20+
- Test: Vitest
- Package Manager: npm

## 프로젝트 구조
src/           → 소스 코드
  ├── index.ts → 엔트리 포인트
  ├── lib/     → 유틸리티, 헬퍼
  ├── types/   → 타입 정의
  └── services/→ 비즈니스 로직
tests/         → 테스트 파일

## 커맨드
- `npm run dev` — 개발 서버 (hot reload)
- `npm run build` — TypeScript 빌드
- `npm test` — 테스트 실행
- `npm run typecheck` — 타입 체크

## 코드 컨벤션
- ESM (import/export) 사용
- 변수/함수: camelCase
- 타입/인터페이스: PascalCase
- 상수: UPPER_SNAKE_CASE
- 파일명: kebab-case.ts
- any 타입 사용 금지
- console.log 대신 structured logger 사용
```

## 슬래시 커맨드

`.claude/commands/plan.md`, `review.md`, `test.md`는
루트의 `commands/` 폴더에서 복사하세요.
