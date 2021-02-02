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
### [자동차 경주 - 단위 테스트](https://github.com/leeyohan93/TIL/blob/master/woowa-techcamp-pro/racingcar.md)
### [로또 - TDD](https://github.com/leeyohan93/TIL/blob/master/woowa-techcamp-pro/lotto.md)
### [JPA](https://github.com/leeyohan93/TIL/blob/master/woowa-techcamp-pro/jwp-jpa.md)
### [인수테스트 주도 개발](https://github.com/leeyohan93/TIL/blob/master/woowa-techcamp-pro/atdd-subway-admin.md)
### [인수테스트 기반 TDD](https://github.com/leeyohan93/TIL/blob/master/woowa-techcamp-pro/atdd-subway-service.md)
### [레거시 코드 리팩터링](https://github.com/leeyohan93/TIL/blob/master/woowa-techcamp-pro/jwp-refactoring.md)

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

### 3. DDD
- 도메인 주도 설계란
- 도메인 주도 설계를 하는 이유
- BOUNDED CONTEXT란
- Aggregate란
- 의존 역전 원칙(DIP)
- 클린 아키텍처

### 4. JPA
- 영속성 컨텍스트와 연관관계
- Auditing

### 5. MySQL 이해하기
```sql
5) SELECT Country, COUNT(*) AS `고객 수` 
1) FROM Customers 
2)	WHERE Country <> 'Norway'
3)		GROUP BY Country
4)			HAVING COUNT(Country) = 1
6)				ORDER BY Country;

1. FROM에서 데이터 집합을 만듭니다. 여기서는 Customers 테이블의 데이터를 가져왔네요.
2. WHERE는 FROM에서 만든 데이터 집합을 조건에 맞게 걸러내는데요.
3. GROUP BY는 WHERE에서 필터링한 (조건에 맞는 데이터를 걸러낸) 데이터를 그룹화합니다.
4. HAVING은 GROUP BY에서 집계한 데이터 집합을 다시 조건에 맞게 필터링할 때 사용해요
5. SELECT에서는 그룹화하고 필터링한 데이터 집합을 집계합니다.
6. 모두 진행한 이후, ORDER BY를 통해 집계한 데이터 집합을 정렬합니다.
```

### 5.1 DB 최적화 대상

- 클라이언트
    - 호출을 줄인다, PreparedStatement(캐싱) , 커넥션 풀, batch size
- 데이터베이스엔진
    - 서버 파라미터를 튜닝합니다. (제일 마지막, 순정이 베스트)
- 파일시스템
    - SSD, **SQL 최적화 (읽어오는 블록 수를 줄인다)**

### 5.2 SQL 최적화 대상
- Table Full Scan
    - 시퀀셜 액세스와 Multiblock I/O 방식으로 디스크를 읽어 한 블록에 속한 모든 레코드를 한번에 읽음
    - 읽을 데이터가 일정량을 넘으면 인덱스보다 Table Full Scan이 유리합니다.
- Index Range Scan
    - 랜덤 액세스와 Single Block I/O로 레코드 하나를 읽기 위해 매번 I/O가 발생
    - 소량 데이터를 주로 검색할때 효과적이다
    - 인덱스 컬럼을 가공하지 않아야 리프 블록에서 스캔 시작점을 찾아 거기서 부터 스캔하다가 중간에 멈출 수 있다.
- Index Full Scan
    - index 컬럼을 사용한 카운트 쿼리 같은 경우
- Index Unique Scan
    - 인덱스가 존재하는 컬럼이 중복값이 입력되어 있지 않고 '='조건으로 찾을때 이 방식으로 처리된다.
- Index를 사용할 경우 최적화 한다는 것은 Random I/O횟수를 줄이는 것이 목표
    - 인덱스 스캔 효율화
    - 랜덤 액세스 최소화 (더 효율적)

### 5.3 테이블 액세스 최소화하는 방법
- 인덱스 컬럼을 추가한다.
- Index Range Scan을 하기 위해서는 인덱스 선두 컬럼이 조건절에 있어야 한다.
(여러 인덱스가 같이 있을 경우 순서에 따라 인덱스가 적용되기도 안되기도 하니 인덱스 순서를 주의해야한다)
- [NOT 비교, LIKE '%?', 인덱스 컬럼이 변경된 경우, 데이터 타입이 다른 경우]는 인덱스를 타지 못한다.
- 인덱스는 항상 정렬 상태를 유지 하므로 인덱스 순서에 따라 OrderBy의 소트 연산을 생략할 수 있다.
- [과세코드, 과세코드 + 이름, 과세코드 + 이름 + 연령]은 모두 중복이다. 마지막 과세코드 + 이름+ 연령을 사용하도록 하자.
- 인덱스를 설계할때 꼭 기억하자
    - 조건절에 항상 사용하거나, 자주 사용 컬럼 선정한다
    - '=' 조건으로 자주조회하는 컬럼을 앞쪽에 둔다
    - Join문, 중첩쿼리 등을 사용할 경우 조건절을 활용하여 조회 범위를 줄이자
