# AttributeConverter 란?

- JPA 환경에서 두개 이상의 엔티 프로퍼티와 한개의 티테이블 컬럼을 매핑 해야할 때 사용하는 클래스.
- 기본적으로는 Entity 의 프로퍼티인 int, String, LocalDate 와 같은 타입은 DB 테이블 한개와 칼럼과 매핑.

## 사용방법
  - AttributeConverter 인터페이스를 구현하고 @Converter 어노테이션으로 빈 등록.
  - 엔티티 Value 객체에 @Convert(converter={ AttributeConverter 인터페이스 구현체 }) 를 등록.
