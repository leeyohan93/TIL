# SessionManagementFilter

## SessionManagementFilter란 ?
- 세션의 보안에 관한 여러가지 일을 하는 필터.

## 주요 기능
- 세션 변조 방지 전략 설정: sessionFixation
    - 기본 서블릿 전략: 요청마다 세션아이디를 change 해주는 전략 
    - 따로 설정하지 않는걸 권장.
- 유효하지 않은 세션을 리다이렉트 시킬 URL 설정
    - `http.sessionManagement().invalidSessionUrl("/login");`
- 동시성 제어: maximumSessions
    - 기본적으로 한 유저의 계정이 무한대로 로그인 할 수있는데 세션 수를 제한할 수 있다.
        ```java
        // 기본적인 전략 : 최근 로그인한 사람이 세션을 점
        - `http.sessionManagement()
                      .maximumSessions(1);`
    
        // 처음 로그인한 유저가 세션 점유 ( 다른 유저가 로그인을 영원히 못할 수도 있다!)
        - `http.sessionManagement()
                      .maximumSessions(1)
                      .maxSessionsPreventsLogin(true);`
        ```
     
- 세션 생성 전략: sessionCreationPolicy
    - ```java
      // 기본전략: 필요할때 만든다.
      http.sessionManagement()
                    .sessionCreationPolicy(SessionCreationPolicy.IF_REQUIRED);
      
      // 세션을 사용하지 않는 전략 (매번 요청마다 로그인을 다시 해야한다) + RequestCacheAwareFilter도 사용 불
      http.sessionManagement()
              .sessionCreationPolicy(SessionCreationPolicy.STATELESS);
      ```
    - 기타 전략: NEVER, ALWAYS 
