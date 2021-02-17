# Converter: 엔티티의 데이터를 변환해서 데이터베이스 컬럼과 매핑하고 싶어요.  

## 요구사항
- 회원 엔티티의 VIP 여부를 boolean 타입으로 사용하면서 데이터베이스 컬럼에 문자 Y 또는 N으로 저장하고 싶다
    - 기존의 경우 데이터베이스 컬럼은 boolean 이 아닌 0 또는 1 인 숫자로 저장된다. (JPA 사용 시)
```text
CREATE TABLE MEMBER (
 ID VARCHAR(255) NOT NULL, 
 USERNAME VARCHAR(255), 
 VIP VARCHAR(l) NOT NULL, 
 PRIMARY KEY (ID)
)
```

## 일반적인 컨버터 사용
-  `AttributeConverter` 인터페이스를 구현하고 `@Converter` 어노테이션으로 빈 등록.
    - `AttributeConverter<X,Y>`에서 X는 엔티티 필드 타입이며 Y는 데이터베이스 컬럼 타입이다.
```java
@Converter
public class BooleanToYNConverter implements AttributeConverter<Boolean, String> {

    @Override
    public String convertToDatabaseColumn(final Boolean attribute) {
        // 엔티티 필드를 데이터베이스 컬럼으로 변환한다.
        return (Objects.nonNull(attribute) && attribute) ? "Y" : "N";
    }

    @Override
    public Boolean convertToEntityAttribute(final String dbData) {
        // 데이터베이스 컬럼을 엔티티 필드로 변환한다.
        return "Y".equals(dbData);
    }
}
```
- Member 엔티티의 vip 필드에 `@Convert` 를 적용.
````java
@Entity
public class Member {

@Id
private String id; 

private String username;

@Convert(converter=BooleanToYNConverter.class) 
private boolean vip;

...
}
````

## 글로벌 설정
- 엔티티 필드에 `@Convert`를 적용하는 대신 글로벌 설정으로 바꿀 수 있다.  
`@Converter(autoApply = true)`
- 글로벌 설정 시 모든 Boolean 타입(AttributeConverter로 구현한 타입)에 대해서 자동으로 컨버터가 적용된다.
- 엔티티 필드에 `@Convert(disableConversion = true)` 옵션을 통해 글로벌 컨버터를 사용하지 않도록 설정도 가능하다.  
```java
@Converter(autoApply = true)
public class BooleanToYNConverter implements AttributeConverter<Boolean, String> {

    @Override
    public String convertToDatabaseColumn(final Boolean attribute) {
        // 엔티티 필드를 데이터베이스 컬럼으로 변환한다.
        return (Objects.nonNull(attribute) && attribute) ? "Y" : "N";
    }

    @Override
    public Boolean convertToEntityAttribute(final String dbData) {
        // 데이터베이스 컬럼을 엔티티 필드로 변환한다.
        return "Y".equals(dbData);
    }

```

## 활용과 주의 사항
- 컨버터를 사용하게 되면 위 요구사항 뿐 만아니라 좀 더 복잡한 요구사항도 해결 할 수 있다. 
    - 두 개 이상의 프로퍼티를 가진 밸류 타입을 한 개 컬럼에 매핑해야할 경우 (`@Embedded`로는 해결할 수 없다.)  
    (ex. Length가 길이 값과 단위의 두 필드를 가지고 있을 때 데이터베이스의 한개 컬럼에 `1000m`와 같은 형식으로 저장하고 싶다.)
```java
public class Length {
    private final int value;
    private final String unit;
}

@Converter
public class LengthConverter implements AttributeConverter<Length, String> {

    @Override
    public String convertToDatabaseColumn(final Length attribute) {
        return attribute.toString();
    }

    @Override
    public Length convertToEntityAttribute(final String dbData) {
        return Length.valueOf(dbData);
    }
}
```


- **밸류 타입의 객체를 `Convert`를 통해 매핑할 경우 꼭 `EqualsAndHashCode`를 오버라이딩 해주어야 한다.**
    - 오버라이딩 하지 않을 시 나는 그저 엔티티를 select 했을 뿐인데 update 쿼리가 발생하는 현상을 보게 될 것이다.  
    [나는 왜 select만 했는데 update 쿼리가 발생할까?](https://jojoldu.tistory.com/536)
