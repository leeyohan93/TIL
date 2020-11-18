# 스프링 시큐리티 - 필터
![](./images/securityFilter.png)  

-  DelegatingFilterProxy
    - 스프링 부트를 쓴다면 자동으로 등록.
    - Bean 이름이 springSecurityFilterChain인 `FilterChanProxy` 객체를 등록하여 필터 처리를 위임.
    
- FilterChainProxy
    - `List<SecurityFilterChain>`를 가지고 있는 객체.
    - 요청에 알맞은 필터를 찾아 적용.
    
- 시큐리티 필터 목록
    - WebSecurity 나 HttpSecurity 설정을 통해 구성됨.
    - WebSecurity docs
    ````The {@link WebSecurity} is created by {@link WebSecurityConfiguration} to create the
         * {@link FilterChainProxy} known as the Spring Security Filter Chain
         * (springSecurityFilterChain). The springSecurityFilterChain is the {@link Filter} that
         * the {@link DelegatingFilterProxy} delegates to.
     ````
  
- 주요 필터 목록
  - UsernamePasswordAuthenticationFilter : 인증 처리
  - FilterSecurityInterceptor : 인가 처리, 필터 순서 마지막에 위치함.
  - ExceptionTranslationFilter : 인증, 인가 Exception 처리.


       


