# JPA 조인 전략 - 외부조인 vs 내부조인
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

##  JPA 조인 전략과 NULL 제약 조건
  위 예제에서 Member를 조회하게 되면 Team도 같이 조회하게 되는데(ManyToOne의 경우 기본 로딩 전략은 즉시로딩) 이 때 실행 SQL은 내부조인이 아닌 외부조인을 사용합니다.  
  ManyToOne 조인 전략이 외부조인인 이유는 ManyToOne 관계에서 Many에 속하지 않은 One이 있을 가능성이 있어 
  멤버 엔티티는 외래키(team)에 대해 기본적으로 null을 허용하고 있기 때문입니다. (기본 값이 nullable = true)   
  만약 기본 조인 전략이 내부조인 일 경우 팀에 속하지 못한 멤버가 있다면 데이터를 정상적으로 조회가 불가능할 것이고 이러한 상황에서 조회 시 JPA 예외가 발생하게 되므로 외부 조인으로 수행해야 합니다.
  
  하지만 '모든 멤버는 각각 하나의 팀에 속한다' 같은 데이터 보장이 되는 상황이라면 외부조인보다 내부 조인이 성능 최적화에서 더 유리하므로 설정을 통해 내부조인으로 변경할 수 있습니다.  
  내부 조인을 사용하기 위한 설정으로는 아래 두가지 방법이 있습니다. (Member에서 Team 외래키에 설정하는 방법)
   - @JoinColumn(nullable = false)
   - @ManyToOne(optional = false)
   
  
## 페치조인 & 엔티티 그래프 기본 조인 전략
   - 페치조인 : 내부 조인을 사용 (`left outer join fetch` 로 외부조인으로 변경 가능)
   - 엔티티그래프 : 외부 조인을 사용 (엔티티 그래프의 경우 nullable, optional 설정을 하더라도 조인 전략이 변하지 않습니다.)
  
## 즉시 로딩 사용 주의사항
   **컬렉션 즉시 로딩은 항상 외부 조인을 사용해야 합니다. (OneToMany)**   
     ManyToOne 관계인 멤버 테이블과 팀 테이블을 조인할 때  
     멤버 테이블의 외래 키(team)에 not null 제약조건을 걸어두면 모든 멤버는 팀에 소속되므로 내부 조인을 사용해도 되지만  
     반대로 팀 테이블에서 멤버 테이블로 OneToMany 관계를 조인할 때 내부 조인을 사용하게 되면 멤버가 한 명 도 없는 팀은 조회되지 않는 문제가 발생합니다.  
     이러한 상황은 데이터베이스 제약조건으로는 막을 수는 없으며 JPA는 OneToMany 관계를 조회할 때는 항상 외부 조인을 사용해야만 합니다.  
     페치조인 또한 기본 전략이 내부 조인이므로 외부조인으로 변경하여 확인하고 사용하거나 엔티티 그래프 조인 전략(외부조인)으로 사용하도록 합시다.

  
출처 : 자바 표준 ORM JPA 프로그래밍 (김영한)
