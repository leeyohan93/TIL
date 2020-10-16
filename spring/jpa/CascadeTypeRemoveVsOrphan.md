# CascadeType.REMOVE vs orphanRemoval = true
 ```java
 @Entity
 public class Parent {
 @Id
@GeneratedValue 
private Long id;
 @OneToMany(mappedBy =”parent”, cascade = CascadeType.REMOVE)
//@OneToMany(mappedBy =”parent”, orphanRemoval = true)
 private List<Child〉 children = new ArrayList<Child>();
 ```
```java
@Entity
public class Child {
@Id 
@GeneratedValue 
private Long id;
0ManyToOne
private Parent parent;
```

- CascadeType.REMOVE
    - 영속성 전이의 옵션중 하나로써 부모 엔티티를 삭제하면 자식엔티티도 삭제하는 기능.
    - 삭제 순서는 외래키 제약조건을 고려해서 자식을 먼저 삭제하고 부모를 삭제한다.
    - 만약 CascadeType.REMOVE 를 설정하지 않고 삭제한다면 부모엔티티에 대한 삭제를 요≠청하는 쿼리가 발생하는데,  
    데이터베이스의 외래키 제약 조건이 걸려있다면 데이터베이스의 외래키 무결성 예외가 발생한다.
    
- 고아객체 (orphan) 제거: orphanRemoval = true
    - 고아객체란 연관관계가 끊어진 객체를 말한다. (참조가 제거된 엔티티)
    - 이 기능으로 부모엔티티의 컬렉션에서 자식 엔티티의 참조만 제거하면 자식엔티티가 자동으로 삭제된다.
    - 이 기능은 참조하는 곳이 하나일 때만 사용해야 하며 이런 이유로 orphanRemoval 옵션은 OneToOne과 OneToMany에만 사용이 가능하다.
    - 추가 기능으로 부모를 제거하면 자식이 고아가된다. 따라서 부모가 제거되면 자식도 제거된다. (=CascadeType.REMOVE) 

- 자주 사용되는 영속성 전이
    - PERSIST : 부모 객체와 함께 자식 객체도 같이 영속화하는데 사용된다.
  
출처 : 자바 표준 ORM JPA 프로그래밍 (김영한)
