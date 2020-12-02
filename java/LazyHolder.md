## 싱글톤 패턴의 LazyHolder 기법

## 싱글톤 패턴이란 ?
- 생성자가 여러 차례 호출되더라도 실제로 생성되는 객체는 하나이고 최초 생성 이후에 호출된 생성자는 최초의 생성자가 생성한 객체를 리턴한다.  
이와 같은 디자인 유형을 싱글턴 패턴이라고 한다.

## LazyHolder 기법
- 싱글톤 패턴을 구현하는 가장 이상적인 형태로 알려진 기법이다.
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
- JVM에게 객체의 초기화 단계를 맡기는 방식으로, 멀티스레드 환경에서도 객체의 단일성을 보장할 수 있다.
- OnlyOne 클래스에는 초기화 할 정적 변수가 없으므로 LazyHolder 내부의 정적 클래스는 호출되기 전까지 초기화되지 않는다.
  이를 통해 싱글톤 객체를 사용할때 까지 로딩을 지연 시킬 수 있으며 쓰레드 safe 하게 사용이 가능하다. 
