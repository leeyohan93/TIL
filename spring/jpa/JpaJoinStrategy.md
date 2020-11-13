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

##  JPA 조인 전략과 NULL 제약 조건
   위 예제에서 Member를 조회하게 되면 Team도 조회하게 되는데 이 때 실행 SQL은 내부조인이 아닌 외부조인을 사용합니다. 
   (ManyToOne 의도 경우 기본 조인 전략이 EAGER)  
  기본 조인 전략이 외부조인인 이유는 Manygit ToOne 관계에서 다에 속하지 않은 일이 있을 가능성이 있기 때문에 
  멤버 테이블에서는 회원 테이블에 TEAM_ID 외래 키에 대해 null 을 허용하고 있기 때문입니다. (기본 값이 nullable = true)   
  만약 팀 속하지 못한 에 회원이 있었을 때 내부조인을 사용하게 된다면 데이터를 정상적으로 조회할 수 없으며 JPA 예외가 발생하게 됩니다.  
  
  하지만 데이터가 보장되있다면 외부조인보다 내부 조인이 성능 최적화에서 더 유리합니다.  
  내부 조인을 사용하기 위한 설정으로는 아래 두가지 방법이 있습니다. (Member에서 Team 외래키에 설정하는 방법)
   - @JoinColumn.nullable = false
   - @ManyToOne.optional = false
   
  
## 페치조인 & 엔티티 그래프 조인 전략
   - 페치조인 : 내부 조인을 사용
   - 엔티티그래프 : 외부 조인을 사용  
   (엔티티 그래프의 경우 nullable, optional 설정을 하더라도 조인 전략이 변하지 않습니다.)
  
## 즉시 로딩 사용 주의사항
   **컬렉션 즉시 로딩은 항상 외부 조인을 사용해야 합니다.**   
     예를 들어 다대일 관계 인 회원 테이블과 팀 테이블을 조인할 때  
     회원 테이블의 외래 키에 not null 제약조건을 걸어두면 모든 회원은 팀에 소속되므로 항상 내부 조인을 사용해도 되지만  
     반대로 팀 테이블에서 회원 테이블로 일대다 관계를 조인할 때 회원이 한 명 도 없는 팀을 내부 조인하면 팀까지 조회되지 않는 문제가 발생합니다.  
     데이터베이스 제약조건으로 이런 상황을 막을 수는 없으며 JPA는 일대다 관계를 즉시 로딩할 때 항상 외부 조인을 사용해야만 합니다.  
     즉시 로딩이 아니더라도 팀에서 멤버를 조인할때 페치조인(내부조인)이 아닌 엔티티 그래프 조인 전략(외부조인)으로 사용해야 합니다.

  
출처 : 자바 표준 ORM JPA 프로그래밍 (김영한)
