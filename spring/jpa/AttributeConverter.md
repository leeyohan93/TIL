# Converter :  커스텀하게 엔티티 필드와 테이블 컬럼을 매핑하고 싶어요.  

## 커스텀하게 엔티티 필드와 테이블 컬럼을 매핑하는 상황 

## AttributeConverter
  - AttributeConverter 인터페이스를 구현하고 @Converter 어노테이션으로 빈 등록.
  - 엔티티 Value 객체에 @Convert(converter={ AttributeConverter 인터페이스 구현체.}) 를 등록.
