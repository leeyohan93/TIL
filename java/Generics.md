# 지네릭스 (Generics)
## 지네릭스란 ? 

- 컴파일 시 타입을 체크해주는 기능
## 제한된 지네릭 클래스

- 지네릭 클래스에서 extends로 대입할 수 있는 타입을 제한

    ```java
    class FruitBox<T extends Fruit>{  // Fruit의 자손만 타입으로 지정 가능
    	ArrayList<T> list = new ArrayList<>();
    	...
    }
    
    // 만약 FruitBox의 지네릭 타입이 T extends Fruit가 아닌 Fruit일 경우 아래 예시도 오류 !
    FruitBox<Apple> appleBox = new FruitBox<Apple>(); // OK. (Apple는 Fruit의 자손)
    FruitBox<Toy> toybox = new FruitBox<>(); // 에러. Toy는 Fruit의 자손이 아님
    ```

- 인터페이스인 경우에도 extends를 사용

    ```java
    interface Eatable {}
    class FruitBox<T extends Eatable> { ... }
    ```

## 지네릭스의 제약

- static 멤버에 타입 변수 사용 불가

    ```java
    class Box<T> {
    	static T item; // 에러
    	static int compate(T t1, T t2) { ... } // 에러
    	...
    }
    ```

- 배열 생성할 때 타입 변수 사용 불가. 타입 변수로 배열 선언은 가능

    ```java
    class Box<T>{
    	T[] itemArr; // OK. T타입의 배열을 위한 참조변수
    	    ...
    	T[] toArray() {
    		T[] tmpArr = new T[itemArr.length]; // 에러. 지네릭 배열 생성 불가
    	}
    }
    ```

## 와일드 카드 <?>

- 하나의 참조 변수로 대입된 타입이 다른 객체를 참조 가능하게 한다.

    ArrayList<? extends Product> list = new ArrayList<Tv>();    //  OK  
    ArrayList<? extends Product> list = new ArrayList<Audio>();    //  OK  
    ArrayList<Product> list = new ArrayList<Tv>();    //  에러. 대입된 타입 불일치

    ```
    <? extends T> 와일드 카드의 상한 제한. T와 그 자손들만 가능
    <? super T>   와일드 카드의 하한 제한. T와 그 조상들만 가능
    <?>           제한 없음. 모든 타입이 가능. <? extends Obejct>와 동일
    ```

- 활용 예

    ```java
    class Juicer {
    	static Juice makeJuice(FruitBox<? extends Fruit> box) {
    		...
    		return new Juice(tmp);
    	}
    }
    
    FruitBox<Apple> appleBox = new FruitBox<>();
    fruitBox<Fruit> fruitBox = new FruitBox<>();
    Jucier.makeJuice(appleBox);
    Jucier.makeJuice(fruitBox);
    ```

## 지네릭 메서드

- 지네릭 타입이 선언된 메서드 (타입 변수는 메서드 내에서만 유효)

    **static <T> void sort(List<T> list, Comparator<? super T> c)**

- 클래스의 타입 매개변수 <T>와 메서드의 타입 매개변수 <T>는 별개
- 메서드를 호출할 때마다 타입을 대입해야 한다. (대부분 생략 가능)

    `FruitBox<Fruit> fruitBox = new FruitBox<Fruit>()`;  
    `FruitBox<Apple> appleBox = new FruitBox<Apple>()`;

    ...

    `Juicer.<Fruit>makeJuice(fruitBox));`  // 타입 생략 가능  
    `Juicer.<Apple>makeJuice(appleBox));`  // 타입 생략 가능

    ```java
    static <T extends Fruit> Juice makeJuice(FruitBox<T> box) {
    	String tmp = "";
    	...
    	return new Juice(tmp);
    }
    ```

## 와일드카드 vs 지네릭 메서드

- 아래 경우 두가지 모두 같은 기능을 제공하지만 애초에 두개의 사용 목적이 다르다.
- **와일드카드** : 와일드 카드는 하나의 참조변수로 서로 다른 타입이 대입된 여러 지네릭 객체를 다루기 위한 것
- **지네릭 메서드** : 메서드를 호출할 때마다 다른 지네릭 타입을 대입할 수 있게 한 것

    ```java
    static Juice makeJuice(FruitBox<? extends Fruit> box) {...}
    static <T extends Fruit> Juice makeJuice(FruitBox<T> box) {...}
    ```

## 지네릭 타입의 형변환

- 지네릭 타입과 원시 타입 간의 형변환은 바람직하지 않다. (경고 발생)  
    ```java능
    Box<Object> objBox = null;
    Box box = (Box) objBox;            // OK. 지네릭타입 → 원시타입    (경고)
    objBox   = (Box<Object>) box;     //  OK. 원시타입    → 지네릭타입 (경고)
    ```

- 와일드 카드가 사용된 지네릭 타입으로는 형변환 가능  
Box<? extends Object> wBox = (Box<? extends Object) new Box<String>();  // OK 형변환 생략 가능.  
- 지네릭 타입은 컴파일 후 모두 제거된다 → 하위 버전과의 호환성 때문.  
지네릭 타입은 제거되기 때문에 각 필요한 부분에 형변환 코드가 추가 된다.

출처 - 자바의 정석
