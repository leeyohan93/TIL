## 스프링과 싱글톤

## 스프링(스프링 컨테이너)과 싱글톤의 관계
- 스프링 컨테이너에 빈 등록을 하게 되면 따로 싱글톤 패턴을 적용하지 않아도 그 객체 인스턴스를 싱글톤으로 보장하고 관리한다.
    - 싱글톤 패턴을 등록하기 위해서 지저분한 코드, 테스트의 어려움 등 문제점이 존재하는데 스프링은 이를 해결해준다.
    
## 왜 싱글톤을 보장하고 관리할까 ?
- 스프링은 태생이 온라인 서비스 기술을 지원하기 위해 탄생했다.
- 대부분의 스프링 어플리케이션은 웹 어플리케이션이며 웹 어플리케이션은 보통 여러 고객에게 동시에 요청을 받는다.
- 만약 스프링 컨테이너가 관리해주는 빈이 싱글톤이 아니라면 요청마다 **객체를 새로 만들게 된다**. (메모리 낭비)

## 싱글톤 방식의 주의점
- 반드시 항상 무상태(stateless)로 설계해야 한다.
    - 싱글톤 방식은 여러 클라이언트가 하나의 같은 객체 인스턴스를 고융하기 때문에 상태를 유지(stateful)하게 설게하면 안된다.
    - A와 B의 요청이 있을때 A에게 응답하기 전에 B의 요청으로 A의 응답이 바뀌게되면 안된다.
- 특정 클라이언트에 의존적인 필드나 값을 변경할 수 있는 필드가 있으면 안된다. (불변)
- 클래스 변수 대신 자바에서 공유되지 않는 지역변수, 파라미터, ThreadLocal 등을 사용해야 한다.
- 스프링 빈의 필드에 공유 값을 설정하면 정말 큰 장애가 발생할 수 있다.

## 스프링은 어떻게 싱글톤을 보장할까 ? + @Configuration
- 스프링컨테이너가 싱글톤을 보장하는 방법은 **바이트 코드를 조작(CGLIB)**하는 방법이다. 
```java
@Configuration
public class AppConfig {

    @Bean
    public MemberService memberService() {
        System.out.println("call AppConfig.memberService");
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
        System.out.println("call AppConfig.memberRepository");
        return new MemoryMemberRepository();
    }
} 
```
- 스프링은 CGLIB라는 바이트코드 조작 라이브러리를 통해 AppConfig 대신 
AppConfig를 상속받은 임의의 다른 클래스를 만들고 그 다른 클래스를 스프링 빈으로 등록하게 된다.
 
- 이렇게 만들어진 AppConfig를 상속한 클래스는 내부적으로 빈 호출 시 이미 빈이 스프링 컨테이너에 등록되어 있다면 
스프링 컨테이너에서 찾아서 반환하고 없다면 기존 로직을 호출해서 새로 생성하고 스프링 컨티이너에 등록 후 반환하는 일을 하며 싱글톤을 보장해준다.

- 그리고 이 CGLIB를 적용하기 위해서는 `@Configuration` 를 적용시켜 주어야 한다. 

- 만약 `@Configuration`를 적용하지 않으면 AppConfig는 CGLIB를 사용된 객체가 아닌 순수 자바 객체(AppConfig.class)로 등록되게 되고 
MemberRepository는 빈 등록할때 한번, memberService()에서 한번 총 두번을 실행하게 된다. (두번이 중요한 것이 아니라 싱글톤을 보장하지 않는 것이 중요.)
    - 이는 싱글톤을 보장하지 않는 것으로 요청(호출)마다 계속 새로운 인스턴스를 생성하게 될 것이다.
    - @Bean만 사용해도 스프링 빈으로 등록되지만 싱글톤은 보장하지 않는다.
    
- 그러므로 스프링 설정 정보는 항상 `@Configuration`를 사용하자.

## 참고
[인프런 - 스프링 핵심 원리 기본편 , 김영한님](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)
