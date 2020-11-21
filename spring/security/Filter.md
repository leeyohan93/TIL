# 스프링 시큐리티 - 필터
## 스프링 시큐리티 필터의 간략한 구조
![](./images/securityFilter.png)  

-  DelegatingFilterProxy
    - 스프링 부트를 쓴다면 자동으로 등록.
    - Bean 이름이 springSecurityFilterChain인 `FilterChanProxy` 객체를 등록하여 필터 처리를 위임.
    
- FilterChainProxy
    - `List<SecurityFilterChain>`를 가지고 있는 객체.
    - 요청에 알맞은 필터를 찾아 적용.
    
## 시큐리티 필터 목록은 어떻게 구성되는가 ?
    - WebSecurity 나 HttpSecurity 설정을 통해 구성됨.
    - WebSecurity docs
        The {@link WebSecurity} is created by {@link WebSecurityConfiguration} to create the
         * {@link FilterChainProxy} known as the Spring Security Filter Chain
         * (springSecurityFilterChain). The springSecurityFilterChain is the {@link Filter} that
         * the {@link DelegatingFilterProxy} delegates to.
     
  
## 주요 필터 목록
  - [WebAsyncManagerIntegrationFilter](https://github.com/leeyohan93/TIL/blob/master/spring/security/filters/WebAsyncManagerIntegrationFilter.md)
    - Spring MVC에서 Async 관련 기능을 사용하여 다른 하위 Thread 에서도 같은 SpringSecurityContextHolder를 공유할 수 있도록 해준다.
  - UsernamePasswordAuthenticationFilter : 인증 처리
  - FilterSecurityInterceptor : 인가 처리, 필터 순서 마지막에 위치함.
  - ExceptionTranslationFilter : 인증, 인가 Exception 처리.
  
  
- 참고
  - [스프링 시큐리티 - 백기선님](https://www.inflearn.com/course/%EB%B0%B1%EA%B8%B0%EC%84%A0-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0/dashboard)


       


