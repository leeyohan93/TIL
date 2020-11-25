# 스프링 시큐리티에서 커스텀한 User 클래스 사용
1. User 클래스를 상속하는 커스텀 유저 클래스 구현.
    - 커스텀한 유저클래스 내부에 유저 엔티티를 가지도록 구현할 수 있다.
    - User 클래스와 엔티티 사이의 어댑터 역할을 함. (엔티티에 의존성을 주지 않는 장점)
    ```java
    public class UserAccount extends User {
    
        private Account account;
    
        public UserAccount(Account account) {
            super(account.getUsername(), account.getPassword(), List.of(new SimpleGrantedAuthority("ROLE_" + account.getRole())));
            this.account = account;
        }
    
        public Account getAccount() {
            return account;
        }
    }
    ```
   
2. UserDetailsService에서 커스텀 유저 클래스를 만들어 반환.
    ```java
    @Service
    public class CustomUserService implements UserDetailsService {
        private final AccountRepository accountRepository; 
    
        @Override
        public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
            Account account = accountRepository.findByUsername(username);
            if (account == null) {
                throw new UsernameNotFoundException(username);
            }
    
            return new UserAccount(account);
        }
    }
    ```
   
3. 컨트롤러의 파라미터에서 어노테이션을 통한 사용
    - `@AuthenticationPrincipal UserAccount userAccount`
        - UserDetailsService 구현체에서 리턴하는 객체를 매개변수로 받을 수 있다.
    - `@AuthenticationPrincipal(expression = "#this == 'anonymousUser' ? null : account") Account account`
        - 익명 Authentication인 경우 null 아닌 경우에는 account 필드를 바로 참조하여 사용한다.
4. 커스텀 어노테이션
    - `@CurrentUser Account account`
    ```java
    @Retention(RetentionPolicy.RUNTIME)
    @Target(ElementType.PARAMETER)
    @AuthenticationPrincipal(expression = "#this == 'anonymousUser' ? null : account")
    public @interface CurrentUser {
    }
    ```
