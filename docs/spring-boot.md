# Spring Boot (Java/Kotlin) 프로젝트 세팅

## 자동 세팅 (복사 후 실행)

```bash
# ============================================
# 프로젝트 초기화 스크립트
# 사용법: bash 에 복붙하세요
# ============================================

PROJECT_NAME="my-spring-app"
PACKAGE="com.example.myapp"
JAVA_VERSION="21"

# 1. Spring Initializr로 프로젝트 생성
curl https://start.spring.io/starter.tgz \
  -d type=gradle-project \
  -d language=java \
  -d javaVersion=$JAVA_VERSION \
  -d groupId=com.example \
  -d artifactId=$PROJECT_NAME \
  -d name=$PROJECT_NAME \
  -d dependencies=web,validation,actuator,lombok \
  -d bootVersion=3.4.0 \
  | tar -xzf -

cd $PROJECT_NAME

# 2. 추가 디렉토리
PACKAGE_DIR="src/main/java/com/example/${PROJECT_NAME//-/}"
mkdir -p $PACKAGE_DIR/{controller,service,repository,model,config,dto}
mkdir -p src/test/java/com/example/${PROJECT_NAME//-/}
mkdir -p .claude/commands

# 3. Health 컨트롤러 예시
cat > $PACKAGE_DIR/controller/HealthController.java << 'EOF'
package com.example.myspringapp.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.Map;

@RestController
public class HealthController {

    @GetMapping("/health")
    public Map<String, String> health() {
        return Map.of("status", "healthy");
    }
}
EOF

# 4. Claude Code 설정
cat > CLAUDE.md << 'EOF'
# Project Context

## 기술 스택
- Language: Java 21
- Framework: Spring Boot 3.4
- Build: Gradle (Kotlin DSL)
- Test: JUnit 5 + MockMvc

## 프로젝트 구조
src/main/java/com/example/myapp/
  ├── controller/    → REST 컨트롤러
  ├── service/       → 비즈니스 로직
  ├── repository/    → 데이터 액세스 (JPA)
  ├── model/         → 엔티티
  ├── dto/           → 요청/응답 DTO
  └── config/        → 설정 클래스
src/main/resources/
  ├── application.yml
  └── application-local.yml
src/test/            → 테스트

## 커맨드
- `./gradlew bootRun` — 개발 서버
- `./gradlew build` — 빌드
- `./gradlew test` — 테스트
- `./gradlew bootJar` — 실행 가능한 JAR

## 코드 컨벤션
- Google Java Style Guide 준수
- 필드 주입(@Autowired) 금지 → 생성자 주입
- Optional 적극 활용
- record로 DTO 정의
- 예외: @ControllerAdvice로 전역 처리
EOF

cat > .claude/settings.json << 'EOF'
{
  "permissions": {
    "allow": [
      "Bash(./gradlew *)",
      "Bash(gradle *)",
      "Bash(java *)",
      "Bash(git *)"
    ]
  }
}
EOF

echo "✅ Spring Boot 프로젝트 세팅 완료!"
echo "   cd $PROJECT_NAME && ./gradlew bootRun"
```

## Kotlin 버전

Spring Initializr에서 `language=kotlin`으로 변경:

```bash
curl https://start.spring.io/starter.tgz \
  -d type=gradle-project-kotlin \
  -d language=kotlin \
  -d javaVersion=21 \
  -d dependencies=web,validation,actuator \
  | tar -xzf -
```

CLAUDE.md의 코드 컨벤션을 Kotlin 스타일로 변경:

```markdown
## 코드 컨벤션
- Kotlin 공식 코딩 컨벤션 준수
- data class로 DTO 정의
- null safety 적극 활용 (!! 금지)
- coroutines 사용 (suspend fun)
- sealed class로 결과 타입 정의
```
