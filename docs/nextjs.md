# Next.js (React + TypeScript) 프로젝트 세팅

## 자동 세팅 (복사 후 실행)

```bash
# ============================================
# 프로젝트 초기화 스크립트
# 사용법: bash 에 복붙하세요
# ============================================

PROJECT_NAME="my-nextjs-app"

# 1. Next.js 프로젝트 생성 (App Router, TypeScript, Tailwind, ESLint)
npx create-next-app@latest $PROJECT_NAME \
  --typescript \
  --tailwind \
  --eslint \
  --app \
  --src-dir \
  --import-alias "@/*" \
  --use-npm

cd $PROJECT_NAME

# 2. 추가 디렉토리
mkdir -p src/{components,hooks,lib,types,services} tests .claude/commands

# 3. 유틸리티 파일
cat > src/lib/utils.ts << 'EOF'
import { type ClassValue, clsx } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
EOF

# 4. 타입 예시
cat > src/types/index.ts << 'EOF'
export interface ApiResponse<T> {
  data: T;
  error?: string;
}
EOF

# 5. Claude Code 설정
cat > CLAUDE.md << 'EOF'
# Project Context

## 기술 스택
- Framework: Next.js 15 (App Router)
- Language: TypeScript (strict)
- Styling: Tailwind CSS
- Test: Vitest + React Testing Library

## 프로젝트 구조
src/
  ├── app/          → 페이지, 레이아웃 (App Router)
  │   ├── layout.tsx
  │   ├── page.tsx
  │   └── api/      → API Routes (Route Handlers)
  ├── components/   → React 컴포넌트
  │   ├── ui/       → 기본 UI 컴포넌트 (Button, Input 등)
  │   └── features/ → 도메인별 컴포넌트
  ├── hooks/        → 커스텀 훅
  ├── lib/          → 유틸리티
  ├── services/     → API 호출, 외부 서비스
  └── types/        → 공용 타입 정의

## 커맨드
- `npm run dev` — 개발 서버 (http://localhost:3000)
- `npm run build` — 프로덕션 빌드
- `npm run lint` — ESLint
- `npm test` — 테스트

## 코드 컨벤션
- 컴포넌트: PascalCase (파일명도 PascalCase.tsx)
- 훅: use 접두사 (useAuth.ts)
- 유틸: camelCase (파일명 kebab-case.ts)
- "use client" 최소화 — 서버 컴포넌트 우선
- Props 타입은 컴포넌트 파일 내에 정의
EOF

cat > .claude/settings.json << 'EOF'
{
  "permissions": {
    "allow": [
      "Bash(npm run *)",
      "Bash(npx *)",
      "Bash(node *)",
      "Bash(git *)"
    ]
  }
}
EOF

# 6. 추가 패키지
npm install clsx tailwind-merge
npm install -D vitest @testing-library/react @testing-library/jest-dom

echo "✅ Next.js 프로젝트 세팅 완료!"
echo "   cd $PROJECT_NAME && npm run dev"
```
