# CsrfFilter

## CsrfFilter 란?
- Csrf 공격을 방지하는 필터.
- CSRF 토큰을 사용하여 의도한 사용자만 리소스를 변경할 수 있도록 허용.

## CSRF((Cross-Site Request Forgery) 란?
![](../images/csrf.jpg)
- 사용자가 자신의 의지와 무관하게 공격자가 의도한 행위를 특정 웹사이트에 요청하게 하는 공격기법.  
- 타 도메인에서 오는 요청을 허용하는 [CORS](https://github.com/leeyohan93/TIL/blob/master/spring/security/Cors.md) 설정을 할 경우 특히주의해야한다.

## 동작 방식
- 기본적으로는 Form 요청을 통해 동작한다.
- 리소스를 변경할 수 있을만한 요청에(사용자가 로그인) CsrfFilter는 Csrf 토큰을 발급해준다.
    - thymeleaf를 사용할 경우 자동으로 Input 태그 hidden 타입으로 csrf 토큰을 넣어 준다.
- 그 후 리소스 변경 요청이 들어올때 Csrf 토큰이 존재하고 일치하는지 검증을 한다.  

## 출처
- https://rusyasoft.github.io/java/2019/02/15/spring-security-csrf-from-context
