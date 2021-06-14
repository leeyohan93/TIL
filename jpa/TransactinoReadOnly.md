# @Transaction(readOnly=true): 읽기 전용 트랜잭션

읽기 전용 트랜잭션은 데이터베이스에서 데이터를 가져와 다른 CUD와 같은 작업을 하지 않는  
Read 작업의 성능 최적화를 위해 사용되는데 이 글은 JPA와 함께 사용할 때의 설명입니다.

## 읽기 전용 설정으로 어떤 부분을 최적화 할 수 있을까?
- JPA의 엔티티가 영속성 컨텍스트를 통해 관리되면 1차 캐시, 변경 감지(더티 체킹) 등 가지는 이점이 많다.
- 하지만 영속성 컨텍스트는 변경 감지를 위해 스냅샷 인스턴스를 보관하기 때문에 더 많은 메모리를 사용하게 된다.
- 단순 조회의 수가 많아질수록 더 많은 메모리가 낭비되기 때문에 최적화해줄 필요가 있다.

## 메모리 최적화 (스냅샷 보관 X)
영속성 컨텍스트가 엔티티를 관리하지 않도록 하여 스냅샷을 생성하지 않아 메모리를 최적화할 수 있다.

- 스칼라 타입으로 조회 (+ DTO 타입 조회) 
    - `select o.id, o.name o.price from Order o`
    
- 읽기 전용 쿼리 힌트 사용
    - 하이버네이트 전용 힌트인 org.hibernate.readonly를 사용한다.
    - ```java
      // JPA
      TypedQuery<Order> query = em.createQuery(select o from Order o , Order.class);
      query.setHint(”org.hibernate.readonly” ， true);
      
      // Spring Data JPA
      @QueryHints(value = @QueryHint(name = "org.hibernate.readOnly", value = "true"))
      Order findByOrdername(String ordername);
      ```
      
## 속도 최적화 (플러시 X)
플러시를 사용하지 않는다면 등록, 수정, 삭제와 같은 동작에서 스냅샷 비교가 일어나지 않으니 속도 최적화에 도움이 된다.

- 읽기 전용 트랜잭션 사용
    - 읽기 전용 트랜잭션은 하이버네이트의 FlushMode를 Manual로 변경하여 트랜잭션이 커밋하더라도 영속성 컨텍스트가 플러시 되지 않도록 한다.
    - 트랜잭션 커밋 과정은 그대로 이루어지며 강제로 플러시를 호출하는 경우가 아니라면 플러시는 일어나지 않는다.
    - `@Transactional(readonly = true》`

- 트랜잭션 밖에서 읽기
    - 트랜잭션 없이 조회하는 방법으로 트랜잭션 자체가 존재하지 않으므로 트랜잭션 커밋과 플러시 또한 호출되지 않는다.
    - JPQL도 트랜잭션 없이 실행하면 플러시를 호출하지 않는다.
    - `@Transactional(propagation = Propagation.NOT_SUPPORTED)`

## 결론
성능 최적화에서는 메모리 최적화와 속도 최적화를 함 적용해주는 것이 좋다.  
**스프링 5.1 버전 이상에서는 `@Transaction(readOnly=true)`로 설정하면, `@QueryHint readOnly`까지 모두 동작한다**.

## 참고
자바 표준 ORM JPA 프로그래밍 (김영한 님)
