# ExceptionTranslationFilter

## ExceptionTranslationFilter란 ?
- 인증, 인가 에러 처리를 담당하는 필터

## 에외 처리 두가지
- AuthenticationException 
    - AuthenticationEntryPoint: 인증이 가능한 페이지(로그인 페이지)로 리다이렉트 
- AccessDeniedException
    - AccessDeniedHandler: 403 에러 반환
    - 유저 친화적인 화면을 위해 따로 설정 권장  
    `http.exceptionHandling().accessDeniedPage("/access-denied)`
    - 서버에 로그를 남기고 싶은 경우 핸들러를 설정하여 로깅 후 페이지 반
    
    
