# ThreadLocal

## 개요

ThreadLocal은 자바 클래스로서 `스레드 로컬 변수`를 제공한다. 스레드 로컬 변수는 get 메서드나 set 메서드를 통해 엑세스하는 각 스레드가 독립적으로 초기화된 변수를 갖는 다는 점에서 일반 변수와
다르다. 스레드 로컬의 인스턴스는 스레드에 연관되되어 있는 private static filed 들이다. (ex. user ID or Transaction ID)

## 예시

```java
 public class ThreadId {
    // Atomic integer containing the next thread ID to be assigned
    private static final AtomicInteger nextId = new AtomicInteger(0);

    // Thread local variable containing each thread's ID
    private static final ThreadLocal<Integer> threadId =
            new ThreadLocal<Integer>() {
                @Override
                protected Integer initialValue() {
                    return nextId.getAndIncrement();
                }
            };

    // Returns the current thread's unique ID, assigning it if necessary
    public static int get() {
        return threadId.get();
    }
}
```
각 스레드는 스레드가 살아 있고 ThreadLocal 인스턴스에 액세스할 수 있는 한 스레드 로컬 변수의 복사본에 대한 참조를 보유합니다. 
스레드가 종료된 후에는 스레드 로컬 인스턴스의 모든 복사본은 가비지 컬렉터의 대상이 됩니다. (이 복사본에 대한 다른 참조가 존재하지 않는 한)

## 주의점

여러 외부 API에 접근하게 되는 경우 비동기, 멀티 스레드로 구현하는 경우가 있는데 다른 스레드 내부에서 스레드 로컬 변수에 접근하게 되면 예외가 발생하게 된다. 
따라서 접근 이전에 지역 변수로 초기화 해놓거나 다른 방법을 통해 접근해야한다. (ex. user Session or RequestContextHolder)

## 참고
https://docs.oracle.com/javase/7/docs/api/java/lang/ThreadLocal.html