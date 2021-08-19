# Monad
~~모나드를 이해하고 나면, 설명하는 방법을 잊어버린다.~~

map은 for문을 옆으로쓰는 거구나, faltmap은 map을 평탄하게 만들어주는 거구나 = 고통의 시작

### 1. Monad의 정의

- Monad는 값을 담는 컨테이너의 일종
- Functor를 기반으로 구현
- flatMap() 메소드를 제공함
- Monad Law를 만족시키는 구현체를 말함

### 2. Monad의 사용 예

- 비동기 연산 처리 (Promise)
- Java의 Optional

### 3. Funtor

Funtor는 단순히 아래의 코드를 나타낸다.

```java
import java.util.function.Function

interface Functor<T> {
	<R> Functor<R> map(Function<T, R> f);
}
```

- Functor를 이용하면 함수들을 손쉽게 합성할 수 있음
    - 값이 없는 케이스 (값이 null 일 때 Optional의 map)
    - 값이 미래에 준비될 것으로 예상되는 케이스
      (비동기 상황에서 동기적인 상황처럼 코딩이 가능함 = 비동기 연산들의 합성이 가능하다)
- Functor로 바라본 map()의 진정한 의미:  ~~컬렉션의 원소를 순회하는 방법~~  
  `<T> 타입의 Funtor를 <R> 타입의 Funtor로 바꾸는 기능`
- List는 단순히 Funtor가 담고 있는 값이 List이다.

### 4. 그럼 Monad는 대체 뭔데?

Monad는 Funtor에 flatMap을 추가한 것

- Functor의 문제점 = flatMap의 추가 이유

```java
FOptional<Integer> num1 = // ...
FOptional<FOptional<Integer>> num2 = // ...

FOptional<Date> date1 = num1.map(t -> new Date(t));
FOptional<Date> date2 = num1.map(t -> new Date(t));    // 컴파일 안됨
```

- flatMap

```java
interface Monad<T,M extends Monad<?, ?>> extends functor<T, M> {
	M flatMap(Function<T, M> f)
}
```

### 5. 결론 - Monad의 의의

값이 없는 상황이나, 값이 미래에 이용가능해질 상황 등,
***일반적으로는 할 수 없는 여러 상황을 모델링 할 수 있도록 하는 것***.

***비동기 로직*** 을 동기 로직을 구현하는 것 과 동일한 형태로 구현하면서도,
***함수의 합성 및 완전한 non-blocking pipeline을 구현할 수 있음***.



### 참고

- [https://www.youtube.com/watch?v=jI4aMyqvpfQ](https://www.youtube.com/watch?v=jI4aMyqvpfQ)
- [https://leadsoftkorea.github.io/2016/03/03/monad-programming-with-scala-future/](https://leadsoftkorea.github.io/2016/03/03/monad-programming-with-scala-future/)
- [https://stackoverflow.com/questions/48895103/why-use-arrows-options-instead-of-kotlin-nullable](https://stackoverflow.com/questions/48895103/why-use-arrows-options-instead-of-kotlin-nullable)
