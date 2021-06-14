# JPQL 실행 시 일부 엔티티에 대한 쿼리만 실행되는 경우

최근 JPA 테스트를 하던 중 한가지 의문스러운 부분을 찾게 되었다.     
JPQL 실행 시 일부 쿼리가 실행이 안되는 것이다.
  
- Team 엔티티
```java
@Getter
@Setter
@Entity
@NoArgsConstructor
public class Team {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @OneToMany(mappedBy = "team", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Member> members = new ArrayList<>();

    public Team(final String name) {
        this.name = name;
    }
}
```

- Member 엔티티
```java
@Getter
@Setter
@NoArgsConstructor
@Entity
public class Member {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @ManyToOne
    @JoinColumn(name = "team_id")
    private Team team;

    public Member(final String name) {
        this.name = name;
    }
}
```

- JPQL 테스트
````java
@Test
void JPQL_실행_시_일부_쿼리가_작동하지_않는다() {
    // given
    Team team = new Team("1팀");
    Member member1 = new Member("1번");
    Member member2 = new Member("2번");

    member1.setTeam(team);
    member2.setTeam(team);
    team.setMembers(Arrays.asList(member1,member2));

    Team savedTeam = teamRepository.save(team);

    // when
    savedTeam.getMembers().get(0).setName("3번");
    Team actual = teamRepository.findByName("1팀");

    // then
    assertThat(actual.getMembers().get(0).getName()).isEqualTo("3번");
}
````

위 테스트는 성공하지만 `Member`에 대한 **업데이트 쿼리는 로그에 발생하지 않았다.**  
JPQL은 실행 전에 영속성 컨텍스트를 플러쉬하니까 변경 감지(더티 체킹)가 일어나서 업데이트 쿼리가 실행되어야 하는데 왜 쿼리가 로그에 안보이지?     
이 원인을 찾아보니 JPA의 구현체인 하이버네이트의 공식 문서에 그 답이 있었다.  
> 6.1. AUTO flush  
By default, Hibernate uses the AUTO flush mode which triggers a flush in the following circumstances:
> - prior to committing a Transaction
> - prior to executing a JPQL/HQL query that overlaps with the queued entity actions
> - before executing any native SQL query that has no registered synchronization  

공식 문서에는 하이버네이트의 플러시 모드는 AUTO이며 위와 같이 세가지 상황에서 플러시가 발생한다고 기재되어있다.  
이 때 두번째 상황을 보고 얻은 결론은 **JPQL은 연관된 엔티티에 대해서만 flush가 작동하며 다른 엔티티에는 작동하지 않는다는 것이다.**  

그 이외에 상황들을 몇개 더 알아보자면
- 현재 예제에서 `Member`와 관련된 JPQL 실행 시 `Member`의 관련 flush가 발생하여 업데이트 쿼리가 발생한다.
- `Team`과 `Member`가 모두 업데이트 된 경우 두 엔티티의 쿼리가 모두 발생한다.
    - `Team` 엔티티의 연관관계 중 cascade = CascadeType.ALL 옵션 때문에.
    [(cascade란?)](https://github.com/leeyohan93/TIL/blob/master/spring/jpa/EntityPersist.md)
- 트랜잭션 커밋, native query 사용 시 엔티티에 관계 없이 영속성 컨텍스트가 모두 flush 된다.
    - **트랜잭션 커밋 시 영속성 컨텍스트가 모두 flush 되므로 실무에서 우리가 따로 크게 신경써주지 않아도 되는 것이다.**

### 참고
- [Hibernate ORM 5.4.26.Final User Guide](https://docs.jboss.org/hibernate/orm/5.4/userguide/html_single/Hibernate_User_Guide.html#flushing)  
