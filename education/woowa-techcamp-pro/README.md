# 우아한 테크캠프 pro
![](./image/우아한테크캠프pro.png)

## 학습 목표
- 클린코드를 통해 유지보수하기 좋은 코드를 구현
- JPA기반의 웹 어플리케이션을 개발하는 경험
- 단위, 인수 테스트를 기반으로 한 웹 어플리케이션 개발
- 레거시 코드를 안정적으로 리팩터링
- 시스템 아키텍처를 설계하고 운영
- 대용량 데이터에 대해 최적화할 대상을 살펴보고, 서버를 튜닝하는 경험

## 진행 미션
### [자동차 경주 - 단위 테스트](https://github.com/leeyohan93/TIL/blob/master/education/woowa-techcamp-pro/racingcar.md)
### [로또 - TDD](https://github.com/leeyohan93/TIL/blob/master/education/woowa-techcamp-pro/lotto.md)
### [JPA](https://github.com/leeyohan93/TIL/blob/master/education/woowa-techcamp-pro/jwp-jpa.md)
### [인수테스트 주도 개발](https://github.com/leeyohan93/TIL/blob/master/education/woowa-techcamp-pro/atdd-subway-admin.md)
### [인수테스트 기반 TDD](https://github.com/leeyohan93/TIL/blob/master/education/woowa-techcamp-pro/atdd-subway-service.md)
### [레거시 코드 리팩터링](https://github.com/leeyohan93/TIL/blob/master/education/woowa-techcamp-pro/jwp-refactoring.md)

## 과정을 통한 배움

### 1. TDD
- TFD(Test First Development) + 리팩토링
- 프로그래밍 의사결정과 피드백 사이의 간극을 의식하고 이를 제어하는 기술
- TDD는 테스트 기술이 아니며 분석기술이며, 설계기술이기도하다.
- 하는 이유: 디버깅 시간을 줄여준다, 동작하는 문서 역할을 한다. 변화에 두려움을 줄여준다.
- TDD 싸이클 실패 → 구현 → 리팩토링
- 테스트하기 어려운 부분을 찾아 가능한 구조로 개선하는 방법
    - 전략 패턴
    - 인터페이스 도출

### 2. ATDD
- 다양한 관점을 가진 팀원들과 협업을 위한 애자일 방법 중 하나로 기획 단계부터 인수 테스트를 통해 공통의 이해를 도모하여 프로젝트를 진행
- 요구사항 → 인수테스트 → 문서화 → TDD → API 개발 → 테스트 리팩터링
- 인수테스트란 사용자의 관점에서 올바르게 작동하는지 테스트 (일반사용자들이 이해할 수 있는 단어 사용)
- 전구간 테스트, 블랙박스 테스트(세부구현에 영향 X)
- 비용을 고려해 해피 케이스의 기능 흐름 검증
- 테스트 환경의 공유되는 자원인 Context나 Database의 독립성을 보장하는 방법
- Test Double: 테스트 목적으로 실제 객체 대신 사용되는 모든 종류의 척도 객체에 대한 일반 용어
    - 왜 하는가? 가짜 객체를 사용함으로써 협력 객체의 행위를 협력객체 스스로가 아닌 테스트가 정의할 수 있도록 하기 위함
- Stubbing: 테스트 동안 호출이 되면 미리 지정된 답변 으로만 응답
- BDD: 행위 주도 개발 , given when then

### 3. [DDD](https://github.com/leeyohan93/TIL/blob/master/ddd/DDD.md)
- 도메인 주도 설계
- 도메인
- 바운디드 컨텍스트
- 컨텍스트 맵
- 애그리거트
- 도메인 서비스
- 의존 역전 원칙
- 클린 아키텍처

### 4. JPA
- 영속성 컨텍스트
- 연관관계
- Auditing

### 5. [MySQL 이해하기](https://github.com/leeyohan93/TIL/blob/master/database/mysql.md) 
- SQL 쿼리의 동작방식 (명렁어 별, select, from, where ..)
- DB 최적화 대상
- RDBM의 블록단위 I/O에 대해
- Single Block I/O와 Multiblock I/O의 차이점
- 인덱스와 테이블 스캔 방식
- 인덱스 사용 시 주의점과 설계할때 고려해야될 사항
- [Mysql DB 엔진 InnoDB의 B+tree에 대하여](https://zorba91.tistory.com/293#recentEntries)
