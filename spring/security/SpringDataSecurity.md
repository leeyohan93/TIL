## 스프링 데이터 시큐리티
- JPQL(@Query)로 principal 참조할 수 있는 기능 제공.

## 기능
- @Query에서 principal 사용.
    - 현재 인증된 유저의 Team을 조회.
    ```java
    public interface TeamRepository extends JpaRepository<Team, Long> {
        @Query("select t from Team t where t.member.id = ?#{principal.user.id}");
        Optional<Team> findCurrentTeam();
    }
    ```
