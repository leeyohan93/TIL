# AttributeConverter 란?

JPA 환경에서 두개 이상의 엔티 프로퍼티와 한개의 티테이블 컬럼을 매핑 해야할 때 (Value 객체와 테이블 칼럼과의 매핑)
사용하는 클래스.
기본적으로는 Entity 의 프로퍼티인 int, String, LocalDate 와 같은 타입은 DB 테이블 한개와 칼럼과 매핑.

- 사용방법
  - AttributeConverter 인터페이스를 구현하고 @Converter 어노테이션으로 빈 등록.
  - 엔티티 Value 객체에 @Convert(converter={ AttributeConverter 인터페이스 구현체 }) 를 등록.
  
- update Query 이슈
  - AttributeConverter 를 통해 구현하고 repository.save(object) 시 insert 쿼리 후 update 쿼리 발생
  - 원인: AttributeConverter 동작 하며 Table Column -> Value 객체를 새로 만들어 기존 save 하려던 Value 객체와 
         equals 메소드를 통한 값 비교를 진행, 비교 값이 false 일 경우 update query 발생.
  - 해결: Value 객체에 equalsAndHashcode 오버라이딩.
