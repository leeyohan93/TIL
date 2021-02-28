# JPA 힌트

JPA 쿼리 힌트는 데이터베이스의 SQL 힌트가 아니라 **JPA 구현체인 하이버네이트에게 제공하는 힌트**이다.

## 1. 사용 예시

대표적인 사용 예로는 하이버네이트의 읽기전용 속성이 있다.
읽기전용 속성을 사용하고 조회하게 되면 영속성 컨텍스트의 스냅샷을 만들지 않아 성능 최적화가 가능하다. 
스냅샷을 만들지 않고 변경감지 또한 작동하지 않기때문에 메모리적인 이점을 얻을 수 있다.

```java
@QueryHints(value = @QueryHint(name = "org.hibernate.readOnly", value = "true"))
Member findReadOnlyByUsername(String username);
```

## 2. 주의점

이러한 읽기전용 속성을 사용할때는 주의할 점이 있다.

- 조회용으로 사용하는 쿼리에 모두 반영 하여도 사실 큰 최적화를 기대하기는 어렵다.
- 성능이 좋지 않은 대부분의 이유는 복잡한 쿼리 자체가 문제일 가능성이 높다.
- 일일이 모든 쿼리에 반영 해주는게 아닌 정말 필요한 곳 몇 군데에 성능테스트를 해보고 사용하자.
- 성능테스트를 통한 확인되는 이점이 있어야 의미가 있는 것이다.
- 처음부터 많은 튜닝을 하는 것은 바람직하지 않다.

### 3. SQL 힌트

그렇다면 앞서 얘기한 SQL 힌트는 무엇일까?

힌트는 SQL 튜닝의 핵심 부분으로 일종의 지시 구문이며 SQL에 포함되어 쓰여져 Optimizer의 실행계획을 원하는 대로 바꿀수 있게 해준다. 데이터베이스의 Optimizer 라고 해서 항상 최선의 실행계획을 수립할 수는 없으므로 테이블이나 인덱스의 잘못된 실행 계획을 개발자가 직접 바꿀 수 있도록 도와주는 것이 SQL 힌트이다.

## 참고

- [https://incheol-jung.gitbook.io/docs/q-and-a/db/sql-hint](https://incheol-jung.gitbook.io/docs/q-and-a/db/sql-hint)
- [스프링 데이터 JPA 실전편 - 김영한님](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EB%8D%B0%EC%9D%B4%ED%84%B0-JPA-%EC%8B%A4%EC%A0%84)
