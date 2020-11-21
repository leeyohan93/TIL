# 스프링 시큐리티와 @Async
- @Async를 사용한 서비스를 호출하는 경우 **쓰레드가 다르기 때문에** SecurityContext를 공유받지 못한다.
- Security configure()를 오버라이드할때 SecurityContextHolder 설정에서 자식 쓰레드에도 공유하는 설정을 추가함으로
 @Async를 수행하는 쓰레드에서도 SecurityContext를 공유받을 수 있도록 할 수 있다. 
  `SecurityContextHolder.setStrategyName(SecurityContextHolder.MODE_INHERITABLETHREADLOCAL);`
  
## 사용 예시 
```java
@RestController
@RequiredArgsConstructor
public class SecurityController {

    private final SecurityService securityService;

    @GetMapping("/async-context")
    public String asyncSecurityContextTest() {
        SecurityLogger.log("Controller");
        securityService.asyncSecurityContextTest();
        return "asyncSecurityContextTest";
    }
}

@Service
public class SecurityService {

    @Async
    public void asyncSecurityContextTest() {
        SecurityLogger.log("SecurityService");
    }
}

public class SecurityLogger {
    public static void log(final String message) {
        System.out.println("Location: " + message);
        System.out.println("Thread: " + Thread.currentThread().getName());
        System.out.println("Principal: " + SecurityContextHolder.getContext().getAuthentication().getPrincipal().toString());
    }
}
```
- 위 로직 실행 시 비동기(@Async)일 경우 SecurityContext 내부의 Authentication이 Null 이어서 NullpointException 이 발생한다.
```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(final HttpSecurity http) throws Exception {
        // 다른 시큐리티 설정들...
        SecurityContextHolder.setStrategyName(SecurityContextHolder.MODE_INHERITABLETHREADLOCAL);
    }
}
```
- SecurityContextHolder에서 비동기 적용 시 공유를하지 못하는 이유는 SecurityContextHolder 기본 전략이 ThreadLocal 이기 때문이다.
- 그러므로 SecurityConfig를 등록할 때 SecurityContextHolder의 전략을 MODE_INHERITABLETHREADLOCAL로 변경하면 SecurityContextHolder 공유가 가능하다.

##참고
[스프링 시큐리티 - 백기선님](https://www.inflearn.com/course/%EB%B0%B1%EA%B8%B0%EC%84%A0-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0/dashboard)
