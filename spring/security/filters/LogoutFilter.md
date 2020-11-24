# LogoutFilter

## LogoutFilter 란 ?
- 여러 LogoutHandler를 사용하여 로그아웃 시 필요한 처리를 하며 로그아웃 후에는 LogoutSuccessHandler를 사용하여 로그아웃 후처리를 한다.
- 다른 핸들러를 추가하거나 SuccessUrl 설정, Cookie, HttpSession 등에 대한 여러 설정이 가능하다.

## 구성
- LogoutHandler
    - CsrfLogoutHandler
    - SecurityContextLogoutHandler

- LogoutSuccessHandler
    - SimpleUrlLogoutSuccessHandler

## 필터 설정 예시
```java
http.logout()
        .logoutUrl("/logout")
        .logoutSuccessUrl("/")
        .logoutRequestMatcher()
        .invalidateHttpSession(true)
        .deleteCookies()
        .addLogoutHandler()
        .logoutSuccessHandler();
```
