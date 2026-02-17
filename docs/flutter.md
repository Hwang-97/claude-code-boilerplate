# Flutter (Dart) 프로젝트 세팅

## 자동 세팅 (복사 후 실행)

```bash
# ============================================
# 프로젝트 초기화 스크립트
# 사용법: bash 에 복붙하세요
# ============================================

PROJECT_NAME="my_flutter_app"

# 1. Flutter 프로젝트 생성
flutter create $PROJECT_NAME --org com.example --platforms ios,android,web
cd $PROJECT_NAME

# 2. 디렉토리 구조 (Feature-First)
mkdir -p lib/{core/{constants,theme,utils,widgets},features,services,models,routing}
mkdir -p .claude/commands

# 3. 핵심 파일
cat > lib/core/constants/app_constants.dart << 'EOF'
class AppConstants {
  static const String appName = 'My App';
  static const String apiBaseUrl = 'https://api.example.com';
}
EOF

cat > lib/core/theme/app_theme.dart << 'EOF'
import 'package:flutter/material.dart';

class AppTheme {
  static ThemeData get light => ThemeData(
        useMaterial3: true,
        colorSchemeSeed: Colors.blue,
        brightness: Brightness.light,
      );

  static ThemeData get dark => ThemeData(
        useMaterial3: true,
        colorSchemeSeed: Colors.blue,
        brightness: Brightness.dark,
      );
}
EOF

# 4. 의존성 추가
flutter pub add go_router riverpod flutter_riverpod dio freezed_annotation json_annotation
flutter pub add -d freezed build_runner json_serializable flutter_lints

# 5. analysis_options 강화
cat > analysis_options.yaml << 'EOF'
include: package:flutter_lints/flutter.yaml

linter:
  rules:
    prefer_const_constructors: true
    prefer_const_declarations: true
    avoid_print: true
    require_trailing_commas: true
    prefer_single_quotes: true
EOF

# 6. Claude Code 설정
cat > CLAUDE.md << 'EOF'
# Project Context

## 기술 스택
- Framework: Flutter 3.x
- Language: Dart
- State Management: Riverpod
- Routing: go_router
- HTTP: dio
- Code Generation: freezed + json_serializable

## 프로젝트 구조 (Feature-First)
lib/
  ├── core/
  │   ├── constants/   → 상수 정의
  │   ├── theme/       → 앱 테마
  │   ├── utils/       → 유틸리티
  │   └── widgets/     → 공용 위젯
  ├── features/        → 기능별 폴더
  │   └── auth/
  │       ├── presentation/ → 화면, 위젯
  │       ├── application/  → 비즈니스 로직 (providers)
  │       ├── domain/       → 모델, 인터페이스
  │       └── data/         → 리포지토리 구현
  ├── services/        → 앱 전역 서비스
  ├── models/          → 공용 모델
  ├── routing/         → 라우팅 설정
  └── main.dart        → 엔트리

## 커맨드
- `flutter run` — 개발 실행
- `flutter build apk` — Android 빌드
- `flutter build ios` — iOS 빌드
- `flutter test` — 테스트
- `dart run build_runner build` — 코드 생성

## 코드 컨벤션
- snake_case (파일명, 변수, 함수)
- PascalCase (클래스, enum)
- const 생성자 우선
- trailing comma 필수
- 위젯은 작게 분리 (100줄 이내)
- print() 금지 → debugPrint() 또는 logger 사용
EOF

cat > .claude/settings.json << 'EOF'
{
  "permissions": {
    "allow": [
      "Bash(flutter *)",
      "Bash(dart *)",
      "Bash(git *)"
    ]
  }
}
EOF

echo "✅ Flutter 프로젝트 세팅 완료!"
echo "   cd $PROJECT_NAME && flutter run"
```
