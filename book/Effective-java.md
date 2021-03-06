## Effective Java 3/E 목차


- [[Item1] 생성자 대신 정적 팩터리 메서드를 고려하라](#Item1.-생성자-대신-정적-팩터리-메서드를-고려하라)


## Item1. 생성자 대신 정적 팩터리 메서드를 고려하라
 * 장점 1.  이름을 가질 수 있다.
    * 기존 생성자와 달리 정적 팩터리메서드는 이름을 가짐으로써 반환 객체의 특성을 묘사할 수 있다.
    * 같은 매개변수 타입과 개수를 가진 생성자를 여러개 만들 수 있다.
 * 장점 2. 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.
    * 상수를 반환하거나 캐싱을 반환함으로 써 성능을 끌어올린다. (플라이 웨이트 패턴과 유사)
 * 장점 3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.
    * 하위 타입의 객체를 반환함으로써 다형성의 유연성을 구현할 수 있다.
    * `java.util.Collections` 같이 하위 타입을 숨기고 최소한의 인터페이스를 제공함으로써 API 사용에 낮은 난이도를 제공한다.
    * 자바 8에서 인터페이스의 public static, 자바 9의 private static 메서드 허용을 활용하면  
    Collections와 같은 동반 클래스(인스턴스화 불가한) 없이 직접 인터페이스가 가질 수 있다.
  * 장점 4. 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
    * 클라이언트는 팩터리가 건네주는 객체가 어떤 것인지 알 필요 없으며 반환클래스의 하위 클래스의 어떤 객체라도 반한활 수 있다.
  * 장점 5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.  
  
  
  * 단점 1. public이나 protected 생성자 없이 정적 팩토리 메서드만 제공하면 상속이 불가능하다.
    * 어찌보면 컴포지션을 사용하도록 유도하고 불변타입으로 만들려면 이 제약이 장점이 될 수 있다.
  * 단점 2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.
    * 아직까지 자바독이 알아서 처리해주지 않기 때문에 알아서 잘 API문서를 통해 정리해야한다.
  * 명명방식
    * from     : 매개변수 하나 받아서 해당 타입의 인스턴스를 반환
    * of       : 여러 매개 변수를 받아 적합한 타입의 인스턴스를 반환
    * valueOf  : from과 of의 더 자세한 버전
    * instance : (매개변수를 받는다면) 매개변수로 명시한 인스턴스 반환, 같은 인스턴스 보장은 하지 않는다. (=getInstance)
    * create   : instance와 같지만, 매번 새로운 인스턴스를 생성해 반환을 보장.(=newInstance)
    * getType  : getInstance와 같으나 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할때 쓴다.  
     `FileStore fs = Files.getFileStore(path)`
    * newType : newInstance와 같으나 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할때 쓴다.
    * type    : getType과 newType의 간결한 버전
  * 핵심 : 정적 팩터리 메서드와 public 생성자는 각자의 쓰임새가 있으니 상대적인 장단점을 이해하고 사용하자.  
    그렇다고 하더라도 정적 팩터리를 사용함에 장점이 많다.
    
