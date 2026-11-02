# WP-TP-API

연구실 GPU 서버 자원 예약 및 실시간 모니터링 시스템의 업무 API를 담당합니다. 회원·인증부터 자원·예약·알림·관리·통계까지 서버의 권한 검사와 데이터 정합성을 구현합니다.

[프로젝트 전체 소개](https://github.com/2026-2-web-programming-term-project/.github#readme)

## 담당 범위

- 연구실 초대 코드, 이메일 인증, 로그인과 일반 사용자·관리자 권한
- 자원 등록·수정·점검, GPU 상태 수신과 연결 이상 판정
- 예약 조회·등록·수정·취소·조기 종료와 동시 예약 충돌 방지
- 임박·운영 취소·대기 알림, 읽음 처리와 재연결 후 누락 알림 조회
- 회원·초대 코드·예약 정책 관리, 본인 이력과 연구실 통계

모든 요청은 소속 연구실과 권한을 기준으로 제한합니다. 예약 시간과 실제 GPU 상태를 구분하며, 예약 변경으로 GPU 프로세스를 자동 실행하거나 강제로 종료하지 않습니다. 대기 알림은 자동 예약을 의미하지 않습니다.

## 현재 설정

| 항목 | 값 |
| --- | --- |
| Java toolchain | 25 |
| Spring Boot | 4.0.0 |
| Gradle Wrapper | 9.8.2 |
| 주요 의존성 | Spring MVC, Security, OAuth2 Client, JPA, Actuator |
| OpenAPI 의존성 | springdoc 3.0.3 |
| DB 드라이버 | MariaDB JDBC |
| 메트릭 의존성 | Micrometer Prometheus registry |
| 애플리케이션 이름 | `TP-API` |

2026-11-02 기준 Spring Boot 초기 프로젝트와 기본 테스트 클래스가 있습니다. 업무 API·데이터 모델·인증 방식·모니터링 수집 경로는 구현 예정입니다. 의존성 선언만으로 OAuth2 인증·OpenAPI·메트릭 공개가 완성된 상태는 아닙니다.

## 개발 실행

Java 25와 사용할 DB가 필요합니다. 현재 설정에는 데이터소스 연결 정보가 없으므로 실행 전 아래 환경변수를 구성합니다.

- `SPRING_DATASOURCE_URL`
- `SPRING_DATASOURCE_USERNAME`
- `SPRING_DATASOURCE_PASSWORD`
- `SERVER_PORT`: 다중 서비스 실행 시 사용할 포트

```bash
./gradlew bootRun
```

```bash
./gradlew test
```

Windows에서는 `gradlew.bat`를 사용합니다. 스키마·테스트 DB 설정과 부트 실행은 구현 단계에서 함께 검증합니다. DB 엔진은 제안서의 MySQL과 현재 MariaDB 드라이버 설정을 검토하여 확정합니다.

## 다중 인스턴스 검증 계획

- 예약 저장 시 충돌·권한·정책을 다시 검사하고, 다중 API 인스턴스의 동시 예약 정합성을 검증합니다.
- 알림·예약 상태 스케줄러의 중복 실행과 이벤트 재처리를 처리합니다.
- 상태·인증 정보가 인스턴스 교체에 종속되지 않도록 저장 방식을 정합니다.
- readiness, DB 연결 수, 오류율, 응답 시간과 확장·축소 시 요청 영향을 관측합니다.

백엔드 제출 마감은 **2026-12-04 23:59 KST**입니다. HA 구성과 성능은 설계·검증 예정입니다.

## 라이선스

[Apache License 2.0](LICENSE)
