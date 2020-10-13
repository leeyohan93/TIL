# JPA 조인 전략
 ```java
 @Entity
 public class Team {
 @Id
@GeneratedValue 
private Long id;
 @OneToMany(mappedBy =”team”)
 private List<Member〉 members = new ArrayList<Member>();
 ```
```java
@Entity
public class Member {
@Id 
@GeneratedValue 
private Long id;
0ManyToOne
@JoinColumn(name = "TEAM_ID")
//@JoinColumn(name = "TEAM_ID", nullable = false)
private Team team;
```

- JPA 조인 전력과 NULL 제약 조건
    - 위 예제에서 Member를 조회하게 되면 Team도 조회하게 되는데 (ManyToOne 의 경우 기본 조인 전략이 EAGER)  
  이 때 실행 SQL에서는 내부조인이 아닌 외부조인을 사용한다.
  그 이유는 현재 회원 테이블에 TEAM_ID 외래 키에 대해 NULL 값을 허용하고 있기 때문이다. (기본 적용 값 : nullable = true)
  기본 값의 이유는 팀에 소속되지 않은 회원이 있을 가능성이 있고 팀에 소속하지 않은 회원과 팀을 내부 조인 하면 팀은 물론 회원 데이터도 조회할 수 없기 때문이다.  
  
  - 이러한 상황을 고려해서 외부 조인을 사용하는 것이지만 외부조인보다 내부 조인이 성능 최적화에서 더 유리하다.
  이때 내부 조인을 사용하기를 원한다면 외래 키에 NOT NULL 제약조건을 설정하여 값이 있는 것을 보장하면 가능하다.
  대신 이 사실을 JPA에게도 알려주어야 하기 때문에 nullable = false 를 통해 알려주거나 
  @ManyToOne.optional = false 설정을 통해 내부 조인을 구현할 수 있다.
  
- 주의사항
    - **컬렉션 즉시 로딩은 항상 외부 조인을 사용한다**.  
     예를 들어 다대일 관계 인 회원 테이블과 팀 테이블을 조인할 때 회원 테이블의 외래 키에 not null 제 약조건을 걸어두면 모든 회원은 팀에 소속되므로
     항상 내부 조인을 사용해도 된 다. 반대로 팀 테이블에서 회원 테이블로 일대다 관계를 조인할 때 
     회원이 한 명 도 없는 팀을 내부 조인하면 팀까지 조회되지 않는 문제가 발생한다.
     데이터베이 스 제약조건으로 이런 상황을 막을 수는 없다. 따라서 JPA는 일대다 관계를 즉시 로딩할 때 항상 외부 조인을 사용한다.

  
출처 : 자바 표준 ORM JPA 프로그래밍 (김영한)
