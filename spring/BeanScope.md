# 빈 스코프 

## 빈 스코프란 ?
- 빈이 존재할 수 있는 범위
- 스프링이 제공하는 여러 빈 스코프
    - **싱글톤** : 기본 스코프, 스프링 컨테이너의 시작과 죵료까지 유지되는 가장 넓은 범위의 스코프.
    - **프로토타입** : 스프링 컨테이너가 빈의 생성과 의존관계 주입까지만 관여하고 더는 관리하지 않는 매우 짧은 범위의 스코프.
    - 웹 관련 스코프
        - **request** : 웹 요청이 들어오고 나갈때 까지 유지되는 스코프. (웹 로깅 용도로 주로 사용)
        - session : 웹 세션이 생성되고 종료될 때 가지 유지되는 스코프. (로그인 관련 용도로 사용)
        - application : 웹의 서블릿 컨텍스트와 같은 범위로 유지되는 스코프.
      
## 싱글톤 타입 스코프
- 빈을 조회하면 스프링 컨테이너는 항상 같은 인스턴스의 스프링을 반환한다.
- [스프링과 싱글톤](https://github.com/leeyohan93/TIL/blob/master/spring/SpringAndSingletone.md)
      
## 프로토 타입 스코프
- 빈을 조회하면 스프링 컨테이너는 항상 새로운 인스턴스를 생성해서 반환한다.
- 스프링 컨테이너가 빈의 생성과 의존관계 주입까지만 관여하고 더는 관리하지 않으며 빈을 관리할 책임은 빈을 주입 받은 클라이언트에 있다.
- `@PostConstruct` 를 통해 빈을 조회할 때마다 호출은 되지만 `@PreDestory` 같은 종료 메서드가 호출되지 않는다. (생성 이후에는 관리하지 않기 때문)

## 싱글톤 빈과 프로토 타입 빈을 함께 사용하고 싶을 때 문제점
- 싱글톤 빈 안에서 프로토타입 빈을 사용하고 싶을때 우리는 보통 싱글톤 빈 내의 프로토 타입 빈이 요청마다 새롭게 생성되기를 기대한다.
- 하지만 싱글톤 빈은 생성 시점에만 의존관계 주입을 받기 때문에, 프로토 타입의 빈은 싱글톤 빈과 함께 유지된다. (프토토 타입 빈이 새롭게 생성되지 않는다.)

## 싱글톤 빈과 프로토 타입 빈을 함께 사용하고 싶을 때 문제점 해결
- 지정한 빈을 컨테이너에서 대신 찾아주는 DL(dependency lookup) 서비스를 제공하는 `ObjectProvider` 를 사용한다.
```java
@RequiredArgsConstructor
public class SingletoneBean{
    private final ObjectProvider<PrototypeBean> prototypeBeanProvider; // ObjectProvider 생성자 주입

    public int logic() {
        PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
        prototypeBean.addCount(); // 기본 count == 0
        int count = prototypeBean.getCount(); 
        return count;
    }
}
```
- loginc()이 실행될 때마다 반환되는 count는 1을 반환할 것이다.

## 빈 스코프와 프록시
- 웹 스코프와 같은 빈을 싱글톤 컨테이너처럼 (일반적인 DI 주입으로) 사용하고 싶다면 프록시 설정을 해주어야한다.
- 그 이유는 request 스코프의 빈일 경우 웹 요청이 있기 전(아직 생성되기 전)에 빈을 주입하려 한다면 에러가 발생하는데, 이를 방지하기 위해 프록시를 사용하면 된다.  
- `@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)`
    - 인터페이스일 경우 `TARGET_CLASS` 대신 `INTERFACES`
- 프록시 객체는 요청이 오면 그때 내부에서 진짜 빈을 요청하는 위임 로직이 들어있다.
- 프록시를 사용하는 핵심은 **진짜 객체를 조회를 꼭 필요한 시점까지 지연처리 한다는 점이다**.

## 참고
[인프런 - 스프링 핵심 원리 기본편 , 김영한님](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard)
