# DefaultLogin-LogoutPageGeneratingFilter

## DefaultLogin-LogoutPageGeneratingFilter란 ?
기본 로그인 폼페이지와 `GET /login` 요청, 로그아웃 폼 페이지와 `GET /logout` 요청을 처리하는 필터.

## 커스터마이징 (LogoutPageGeneratingFilter도 매우 유사)
- 기본 폼 페이지에서 파라미터 이름 커스터마이징.
```java
 http.formLogin()
                .usernameParameter("my-username")
                .passwordParameter("my-password");
```
- 커스텀한 로그인 페이지로 연결. (기본 제공해주는 로그인, 로그아웃 페이지가 제공되지 않으며 DefaultLogin-LogoutPageGeneratingFilter 또한 제공되지 않는다.)
```java
 http.formLogin()
                .loginPage("/signin")
                .permitAll();
```
- 이외에도 성공, 실패에 대한 `handler` 및 포워딩 url 설정 또한 커스텀 가능하다.  
