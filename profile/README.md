# AI 시대의 소프트웨어 개발

> 알아야 지시하고, 알아야 검증한다

**AI는 내가 말한 것을 만들지, 내가 원한 것을 만들지 않는다.**

이 시리즈는 블로그 하나를 두 번 만듭니다. 1부에서는 블로그가 돌아가는 바탕을 손으로 직접 만들고, 2부에서는 같은 블로그를 AI에게 맡기며 무엇을 어떻게 요구하고 결과를 어떻게 검증하는지 보여 줍니다. 요청 하나가 서버 안에서 어떻게 처리되는지 알아야 AI가 만든 코드가 맞는지 판단할 수 있기 때문입니다.

지은이: 마르코

| | 1부 | 2부 |
| --- | --- | --- |
| 제목 | 웹 애플리케이션은 어떻게 동작하는가 | AI 시대의 소프트웨어 개발 |
| 다루는 것 | 자바로 블로그의 바탕을 직접 만들기 | 같은 블로그를 AI와 함께 키우기 |
| 준비물 | JDK 21, Maven, Git, 10장부터 Docker (부록 A에서 안내) | JDK 21 (빌드는 Maven Wrapper가 맡음) |
| 구성 | 12장, 부록 2개 (773쪽) | 프롤로그, 24장, 에필로그, 부록 5개 (812쪽) |
| 미리보기 | [1부 미리보기](https://github.com/backend-blog/.github/blob/main/profile/dist/part1-preview.pdf) (머리말~2장, 144쪽) | [2부 미리보기](https://github.com/backend-blog/.github/blob/main/profile/dist/part2-preview.pdf) (머리말~2장, 86쪽) |

> 미리보기에는 머리말, 차례, 이 책의 구성과 앞의 두 장이 들어 있습니다. 2부 미리보기에는 프롤로그도 들어 있습니다. 출간 전 원고라 내용이 바뀔 수 있습니다. 원고와 실습 저장소는 출간할 때 공개합니다.

2부만 읽어도 흐름을 따라갈 수 있도록 썼습니다. 다만 1부에서 직접 만든 부품이 2부에서 AI가 만든 코드를 읽는 바탕이 되므로, 1부부터 읽으면 더 많은 것이 보입니다.

---

## 1부 웹 애플리케이션은 어떻게 동작하는가

주인공 두원은 터미널에서만 실행되는 작은 블로그에서 시작해, 블로그가 부딪히는 문제를 장마다 하나씩 풀며 웹 애플리케이션 하나를 완성합니다. JAR로 묶고, 로그를 남기고, 소켓으로 HTTP 서버를 만들고, Servlet과 JDBC로 블로그를 올린 뒤 Spring MVC로 옮깁니다. 마지막 장에서는 직접 만든 부품이 Spring 안에서 어떤 이름으로 동작하는지 짝지어 봅니다. 명령은 macOS·Linux용과 Windows용을 나눠 적었습니다.

| 장 | 제목 | 주요 내용 | Design Pattern·SOLID | 실습 저장소 |
| --- | --- | --- | --- | --- |
| 1 | 빌드와 패키징 | `javac`와 `java`로 컴파일과 실행, classpath와 manifest, 실행 가능한 JAR, Maven | - | `build-and-packaging` |
| 2 | 로깅 | SLF4J와 Logback, 로그 레벨, `logback.xml`, 여러 스레드가 남긴 로그 읽기 | Facade | `logging` |
| 3 | "주소 좀 줘": 소켓 하나로 만드는 웹 서버 | 단일 스레드 웹 서버, HTTP 메시지 읽기, `Content-Length`와 상태 코드 실험 | Decorator | `simple-http-server` |
| 4 | "한참 돌다가 떴어": 느린 손님 하나와 일꾼들 | 요청마다 만드는 스레드, thread pool, 스레드 수와 작업 큐의 상한 | Producer-Consumer | `simple-http-server` |
| 5 | "글은 어디서 읽어?": 요청 객체와 서비스, context, 글쓰기 폼 | 요청·응답 객체, 계약 테스트, annotation과 reflection으로 만드는 context, POST와 303 redirect | Template Method, Singleton, PRG · SRP, OCP, LSP | `simple-http-server` |
| 6 | Servlet 1: CGI에서 Servlet까지 | Tomcat, 기능별 Servlet, 세션과 쿠키로 하는 로그인, JSP | Template Method | `servlet-blog` |
| 7 | Servlet 2: 필터와 프런트 컨트롤러 | 필터, FrontServlet, `PostRepository` 인터페이스와 계약 테스트 | Chain of Responsibility, Front Controller, Strategy · DIP, LSP | `servlet-blog` |
| 8 | Servlet 3: 컨트롤러는 평범한 객체 | 컨트롤러 객체, 스스로 규칙을 확인하는 객체, 회귀 테스트, multipart | Command, MVC · SRP, OCP, LSP | `servlet-blog` |
| 9 | 제어를 넘기다: IoC, DI, AOP | `ServletContainerInitializer`, 의존성 주입, 테스트 대역, dynamic proxy | Proxy, Decorator · OCP | `servlet-blog` |
| 10 | JDBC: 글을 데이터베이스에 저장하다 | MySQL, SQL 주입, 페이지 나누기, transaction과 격리 수준 | ISP, OCP | `servlet-blog` |
| 11 | connection pool과 transaction proxy: 몰려드는 요청을 버티다 | connection pool 직접 만들기, DBCP2, `JdbcTemplate`, transaction proxy | Template Method | `servlet-blog` |
| 12 | Spring MVC로 옮기기: 직접 만든 것들은 어디로 갔나 | 직접 만든 부품과 Spring 기능 짝짓기, 요청 처리 흐름, WAR 배포 | Front Controller, Adapter, Strategy | `spring-blog` |

부록: A. 개발 환경 설치 / B. 실습 저장소 안내

디자인 패턴과 SOLID 원칙은 원어로 적었습니다. SRP는 Single Responsibility Principle(단일 책임), OCP는 Open-Closed Principle(개방-폐쇄), LSP는 Liskov Substitution Principle(리스코프 치환), ISP는 Interface Segregation Principle(인터페이스 분리), DIP는 Dependency Inversion Principle(의존성 역전)입니다. 1부에서 처음 이름을 붙인 패턴과 원칙을 2부에서는 AI에게 지시하고 AI의 코드를 리뷰하는 말로 다시 씁니다.

### 1부 실습 저장소

| 저장소 | 장 | 브랜치 |
| --- | --- | --- |
| [`build-and-packaging`](https://github.com/backend-blog/build-and-packaging) | 1장 | `step1`~`step4` |
| [`logging`](https://github.com/backend-blog/logging) | 2장 | `step1`~`step4` |
| [`simple-http-server`](https://github.com/backend-blog/simple-http-server) | 3~5장 | `step1`~`step8` |
| [`servlet-blog`](https://github.com/backend-blog/servlet-blog) | 6~11장 | `step1`~`step14` (곁가지 `step5-file` 포함) |
| [`spring-blog`](https://github.com/backend-blog/spring-blog) | 12장 | `step1`~`step3` |

단계마다 브랜치가 두 개 있습니다. `stepN-exercise`는 그 단계를 시작할 때의 코드로, 채울 곳에 `TODO`가 적혀 있습니다. `stepN-solution`은 그 단계를 마친 코드입니다.

```bash
git clone https://github.com/backend-blog/logging.git
cd logging
git switch step1-exercise

# 단계를 마치면 완성 브랜치와 비교합니다
git diff origin/step1-solution -- src
```

---

## 2부 AI 시대의 소프트웨어 개발

1부에서 블로그를 직접 만든 두원이 같은 블로그를 AI에게 맡기며 시작합니다. "블로그 만들어 줘" 한 줄로 받은 블로그(v0)가 부딪히는 문제를 장마다 하나씩 풀어, 여러 사람이 쓰고 몇 달을 운영하는 블로그(v15)로 키웁니다. 스물네 장은 일곱 묶음과 마지막 돌아보기 장으로 이루어져 있습니다. 장마다 "같은 기능, 두 가지 요청"에서 그냥 요청한 결과와 정해서 요청한 결과를 코드로 비교합니다.

| 묶음 | 장 | 제목 | Design Pattern·SOLID | 블로그 버전 |
| --- | --- | --- | --- | --- |
| | 프롤로그 | AI는 시킨 대로 만든다. 원하는 대로 만드는 건 사람의 몫이다 | | |
| 시킨 대로 만든다 | 1 | "블로그 만들어 줘": 10분 만에 생긴 블로그 | - | v0 |
| | 2 | "임시저장도 넣어 줘" 했더니 미완성 글이 목록에 나왔다 | - | v1 |
| 원하는 것을 정확히 말하기 | 3 | 내가 뭘 원하는지 모를 때: AI에게 나를 인터뷰시키기 | - | v2 |
| | 4 | 어디까지 한 번에 맡길까: 쪼개서 말하기 | Repository | v2 |
| | 5 | "API 만들어 줘" 했더니 `/getPost`와 `/deletePost`가 생겼다 | - | v2 |
| | 6 | "발행 시각 없는 발행 글"이 생겼다: 지켜야 할 규칙 | - | v2 |
| 안전망 | 7 | 명세를 테스트로: 사람이 테스트를, AI가 구현을 | - | v2 |
| | 8 | 기계가 먼저 보는 품질: 린트와 SonarQube | - | v2 |
| 바뀌어도 무너지지 않는 구조 | 9 | "댓글 넣어 줘" 했더니 서비스가 800줄이 됐다: 단일 책임 원칙 | Iterator · SRP | v3 |
| | 10 | DB로 바꿨더니 테스트는 통과, 실제로는 실패했다: 리스코프 치환 원칙과 의존성 역전 원칙 | Adapter · LSP, DIP | v4 |
| | 11 | 기존 코드를 건드리지 않고 기능 늘리기: 개방-폐쇄 원칙과 인터페이스 분리 원칙 | Strategy, Observer, Decorator · OCP, ISP | v5 |
| | 12 | 원칙으로 AI를 지시하고 리뷰하기 | SRP, OCP, LSP, ISP, DIP (다섯 원칙 정리) | v5 |
| 혼자에서 팀으로 | 13 | AI 시대의 애자일 (1): 스프린트를 계획하고 나눠 맡기기 | - | v6 |
| | 14 | AI 시대의 애자일 (2): 스프린트를 끝내고 돌아보기 | - | v6 |
| | 15 | 다른 사람이 이어받는 블로그 | - | v7 |
| 진짜 사용자를 만나다 | 16 | "사진도 넣게 해 줘" 했더니 배포할 때마다 사진이 사라졌다 | - | v8 |
| | 17 | "빠르게 해 줘" 했더니 내린 글이 계속 보였다 | Decorator (캐시) | v9 |
| | 18 | "예약 발행해 줘" 했더니 알림이 두 번 갔다 | Outbox, Decorator | v10 |
| | 19 | GitHub로 로그인했더니 남의 계정이 열렸다 | Circuit Breaker, Decorator | v11 |
| 오래 운영하고 이어 가기 | 20 | "블로그가 안 열려요": 장애 대응과 회고 | - | v12 |
| | 21 | "오래된 댓글 정리해 줘" 했더니 운영 데이터가 사라졌다 | - | v13 |
| | 22 | "동아리 게시판을 합쳐 줘": 남이 만든 오래된 코드 이어받기 | Anti-Corruption Layer, Strangler Fig | v14 |
| | 23 | "영어로도 읽게 해 줘": 여러 AI와 함께 일하기 | - | v15 |
| 돌아보기 | 24 | v0에서 v15까지: 과정을 돌아보고 설명하기 | - | v0~v15 |
| | 에필로그 | AI가 더 좋아져도 남는 것 | | |

부록: A. AI와 개발해도 알아야 할 열일곱 가지 / B. 작업 지시서와 요청 모음 / C. 리뷰 질문, 완료의 정의, 점검표 / D. 용어집 / E. 예제 저장소 안내

### 2부 예제 저장소

[`blog-with-ai-source`](https://github.com/backend-blog/blog-with-ai-source)에는 블로그 버전마다 브랜치(`v0`~`v15`)가 있습니다. 읽고 있는 장의 버전으로 바꿔 실행해 봅니다.

```bash
git clone https://github.com/backend-blog/blog-with-ai-source.git
cd blog-with-ai-source
git switch v2
./mvnw spring-boot:run        # Windows에서는 mvnw.cmd spring-boot:run
```

---

## 라이선스

1부와 2부의 실습·예제 코드는 MIT 라이선스로 공개합니다. 누구나 자유롭게 쓰고, 고치고, 다시 배포할 수 있습니다.
