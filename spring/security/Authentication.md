# 스프링 시큐리티 - 인증 아키텍처
## SecurityContextHolder & Authentication
![](./images/SecurityContextHolder.png)
- **SecurityContextHolder**
    - 누가 인증 되었는지에 대한 세부 정보를 저장하는 곳
    - SecurityContext를 제공하며 기본적으로 ThreadLocal 사용.
- **SecurityContext**
    - Authentication 제공.
- **Authentication**
    - 인증된 사용자 정보인 Principal(인증 주체)과 GrantAuthority(주체 권한) 제공.  
- **Principal**
    - 인증한 사용자에 해당하는 정보. UserDetailsService에서 반환한 객체이며, UserDetails 타입.
- **GrantAuthority**
    - Principal(인증한 사용자)가 가지고 있는 권한(“ROLE_USER”, “ROLE_ADMIN”), 인증 이후 인가 및 권한 확인 시 사용
- **UserDetails**
    - 애플리케이션이 가지고 있는 유저 정보(User)와 스프링 시큐리티가 사용하는 Authentication 객체 사이의 어댑
- **UserDetailsService**
    - 스프링 시큐리티에게 유저 정보를 제공하는 서비스. 인증을 하는 서비스는 아님. 인증은 AuthenticationManager가 한다.  
- **ThreadLocal**
    - 한 쓰레드 내에 공유하는 자원.
