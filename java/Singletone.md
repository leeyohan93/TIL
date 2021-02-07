## 싱글톤 패턴

## 싱글톤 패턴이란 ?
- 생성자가 여러 차례 호출되더라도 실제로 생성되는 객체는 하나이고 최초 생성 이후에 호출된 생성자는 최초의 생성자가 생성한 객체를 리턴한다.  
이와 같은 디자인 유형을 싱글턴 패턴이라고 한다.
- 많은 요청에도 싱글톤으로 만들어진 객체를 공유해서 사용하기 때문에 매번 객체를 생성하는 일을 하지 않아도 된다. -> 메모리 낭비를 하지 않는다.  

## 싱글톤 패턴의 문제점
- 싱글톤 패턴을 구현하는 코드 자체가 많이 들어간다.
- 의존관계상 클라이언트가 구체 클래스에 의존한다. DIP를 위반한다. 클라이언트가 구체 클래스에 의존해서 OCP 원칙을 위반할 가능성이 높다. 테스트하기 어렵다.
- 내부 속성을 변경하거나 초기화 하기 어렵다.
- private 생성자로 자식 클래스를 만들기 어렵다.
- 결론적으로 유연성이 떨어진다.
- 안티패턴으로 불리기도 한다.

## 싱글톤 방식의 주의점
- 무상태(stateless)로 설계해야 한다. (공유하여 사용하기 때문에)
- 특정 클라이언트에 의존적인 필드가 있으면 안된다.
- 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다.
- 가급적 읽기만 가능해야 한다.
- 필드 대신에 자바에서 공유되지 않는, 지역변수, 파라미터, ThreadLocal 등을 사용해야 한다.
- 만약 스프링 빈의 필드에 공유 값을 설정하면 정말 큰 장애가 발생할 수 있다.

## 싱글톤을 구현하는 방법

### 객체를 미리 생성해두는 기법
- 객체를 미리 생성해두는 가장 단순하고 안전한 방법
- private 생성자를 사용해서 외부에서 임의로 new 키워드를 사용하지 못하도록 막아야 한다.
```java
public class Something{ 
    private static final Something something = new Something();
        
    private Something() {}

    public static Something getInstance(){ 
        return something;
    }
} 
```

### LazyHolder 기법
- 싱글톤 패턴을 구현하는 가장 이상적인 형태로 알려진 기법이다.
- Somthing 클래스에는 초기화 할 정적 변수가 없으므로 LazyHolder 내부의 정적 클래스는 호출 전까지 초기화되지 않는다.
  이를 통해 싱글톤 객체를 사용할때 까지 로딩을 지연 시킬 수 있으며 쓰레드 safe 하게 사용이 가능하다. 
```java
public class Something{ 
    private Something() {}

    private static class LazyHolder{ 
        static final Something INSTANCE = new Something(); 
    }

    public static Something getInstance(){ 
        return LazyHolder.INSTANCE; 
    }
} 
```
