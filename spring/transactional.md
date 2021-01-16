# 스프링 @Transactional과 트랜잭션 전파 

## @Transactional 이란?

스프링 프레임워크에서의 트랜잭션 관리는 보통 @Transactional 어노테이션을 사용하여 관리합니다.
@Transactional을 통해 전파, 격리, 시간 초과, 읽기 전용 및 롤백 조건을 설정할 수 있으며 트랜잭션 관리자를 지정할 수도 있습니다.

이렇게 간단하게 어노테이션 하나로 트랜잭션을 관리할 수 있는 이유는 스프링 AOP 덕분인데 사용 시에 몇가지  주의 사항이 존재합니다.
<br/><br/>

## @Transactional 사용 시 주의사항

@Transactional 사용 시 주의사항은 내부(같은 Bean)에서 @Transactional이 명시된 다른 메서드를 호출하게 되면 트랜잭션이 정상적으로 동작하지 않는 다는 것입니다.
또한, private 메서드에도 마찬가지로 @Transactional이 동작하지 않습니다.

### 문제 예시

```java
@Service
public class UserService {
    private UserRepoistory userRepoistory;

    @Transactional
    public User create(User user) {
        return userRepoistory.save(user);
    }

    public User createWithoutTransaction(User user) {
        return this.create(user);
    }
}
```

위와 같은 서비스가 있을때 UserController에서는 userService.create()를 실행하면 트랜잭션이 동작하지만 userService.createWithNoTransaction()을 실행하면 트랜잭션이 동작하지 않습니다.

### 문제 원인

원인을 간략하게 정리하자면 외부 메서드(외부 Bean)에서 호출하는 경우에만 프록시가 인터셉트되어 동작하며 프록시에서 @Transcational이 동작하는 것이며 내부(같은 Bean)에서 호출하는 경우에는 프록시가 인터셉트되지 않아 @Transcational이 동작하지 않는 것입니다.

아래는 [스프링 공식문서](https://docs.spring.io/spring-framework/docs/current/reference/html/data-access.html#transaction-declarative-annotations) 에 기재되어 있는 내용입니다.

> In proxy mode (which is the default), only external method calls coming in through the proxy are intercepted. This means that self-invocation (in effect, a method within the target object calling another method of the target object) does not lead to an actual transaction at runtime even if the invoked method is marked with @Transactional. Also, the proxy must be fully initialized to provide the expected behavior, so you should not rely on this feature in your initialization code (that is, @PostConstruct).

<br/>

## 트랜잭션 전파

### REQUIRED (Default)

```java
@Transactional(propagation = Propagation.REQUIRED)
public void doSomthing() { ... }
```

- 부모 트랜잭션이 존재한다면 부모 트랜잭션으로 합류합니다.
- 부모 트랜잭션이 없다면 새로운 트랜잭션을 생성합니다.
- 중간에 롤백이 발생한다면 모두 하나의 트랜잭션이기 때문에 진행사항이 모두 롤백 됩니다.

### REQUIRES_NEW

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void doSomthing() { ... }
```

- 항상 새로운 트랜잭션을 생성합니다.
- 기존 트랜잭션 여부에 상관없이 새로운 트랜잭션이 생성되므로 각각의 트랜잭션이 롤백되더라도 서로 영향을 주지 않습니다.

### MANDATORY

```java
@Transactional(propagation = Propagation.MANDATORY)
public void doSomthing() { ... }
```

- 부모 트랜잭션에 합류합니다.
- 부모 트랜잭션이 없다면 예외를 발생시킵니다.
- 독립적으로 실행 할 수 없는 경우에 주로 설정합니다.

### SUPPORTS

```java
@Transactional(propagation = Propagation.SUPPORTS)
public void doSomthing() { ... }
```

- 부모 트랜잭션이 있다면 합류합니다.
- 진행중인 부모 트랜잭션이 없다면 트랜잭션을 생성하지 않고 실행합니다.

### NOT_SUPPORTED

```java
@Transactional(propagation = Propagation.NOT_SUPPORTED)
public void doSomthing() { ... }
```

- 부모 트랜잭션이 존재할 경우 부모 트랜잭션을 보류 시킵니다.
- 부모 트랜잭션 유무에 상관없이 트랜잭션을 생성하지 않고 실행합니다.

### NEVER

```java
@Transactional(propagation = Propagation.NOT_SUPPORTED)
public void doSomthing() { ... }
```

- 트랜잭션을 생성하지 않고 실행합니다.
- 부모 트랜잭션이 존재한다면 예외를 발생시킵니다.
<br/><br/>

### 참고

- [스프링 공식문서](https://docs.spring.io/spring-framework/docs/current/reference/html/data-access.html#transaction-declarative-annotations)
- [https://www.baeldung.com/spring-transactional-propagation-isolation](https://www.baeldung.com/spring-transactional-propagation-isolation)
- [https://jsonobject.tistory.com/467](https://jsonobject.tistory.com/467)
