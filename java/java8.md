# 자바 8

## 자바 8의 주요한 특징

### 람다
- 익명 클래스의 필요성을 제거하고 매우 간단하게 함수형 프로그래밍을 사용 가능
- 함수형 프로그래밍이란 함수를 이용해서 **사이드 이펙트 없도록** 선언형 프로그래밍을 이용하는 것이다, 상태를 가지지 않는 것이 OOP와의 차이점이다.

### 함수형 인터페이스
- 하나의 메소드만 선언되어 있는 인터페이스
- 인터페이스 선언시 @FunctionalInterface 어노테이션을 사용하면 해당 인터페이스는 내용이 없는 하나의 메소드만 선언 할 수 있도록 제한할 수 있다. 
- 메서드가 하나로 지켜지지 않을 경우 컴파일 오류가 발생한다.

### 인터페이스 default 메서드
- default 메서드를 만든 이유는 '하위 호환성' 때문이다. 많은 사람이 사용하고 있는 인터페이스에 새로운 메소드를 추가해야 할 때 기존 방식대로 추가하면 이미 사용하고 있는 사람들은 전부 오류가 발생하고 수정해야하는 일이 발생한다. 
  이럴 때 사용하는 것이 default 메소드이며 함수형 인터페이스를 유지하기 때문에 람다 표현식을 사용하는데에도 도움을 준다.  
  (람다 표현식을 사용하려면 메소드가 1개여야 하는데 그 이외의 메소드는 default method로 구성되게 만들면 되기 때문이다)

### 스트림
- 자바의 자료구조들을 선언적으로 다루는 역할을 하며 람다를 활용할 수 있는 기술이다.
- Stream은 Collection 객체에 사용할 수 있는 함수형 인터페이스를 통한 간단한 내부 데이터 순회 방법이다.
- 병렬 연산 처리시 공유된 쓰레드 풀을 사용하기 때문에 성능 장애를 일으킬 수 있따.
- 스트림과 컬렉션의 차이는 데이터를 계산 시점의 차이이다. 외부반복, 내부반복

### 옵셔널
- null 처리를 보다 간편하게 하기 위해서 만들어졌다.