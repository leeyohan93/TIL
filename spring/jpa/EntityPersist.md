# 부모 엔티티에서 자식 엔티티를 관리하고 싶어요. (feat.DDD)

## 어떻게 하면 부모 엔티티에서 자식 엔티티를 관리 (생성, 수정, 삭제)를 할 수 있을까 ?
- 결론부터 말하자면 부모 엔티티의 연관관계에  `CascadeType.ALL`와 `orphanRemoval = true`를 설정해주면 된다.  
- 두 옵션을 모두 활성화하면 부모 엔티티에서 자식의 생명주기를 관리할 수 있다.
    - 자식을 저장하려면 부모에 등록만 하면 된다(cascade).
    - 자식을 삭제하려면 부모에서 제거하면 된다(orphanRemoval). 
 ```java
 @Entity
 public class Order {
 
     @Id
     @GeneratedValue
     private Long id;
 
     @OneToMany(mappedBy = "order", cascade = CascadeType.ALL, orphanRemoval = true)
     private List<LineItem> lineItems = new ArrayList<>();
 }

@Entity
public class LineItem {

    @Id
    @GeneratedValue
    private Long id;
    
    @ManyToOne
    @JoinColumn(name = "order_id")
    private Order order;
}
```

## 자식을 저장하려면 부모에 등록만 하면 된다(cascade).
- `CascadeType.PERSIST`와 `CascadeType.MERGE` 옵션은 부모 엔티티에서 저장이나 수정이 일어났을 때 연관 엔티티까지 수행 동작을 전파하는 역할을 한다.
    
## 자식을 삭제하려면 부모에서 제거하면 된다(orphanRemoval)
- JPA에서는 고아객체(연관관계가 끊어진 객체)를 다룰 수 있는 옵션이 있는데 이 옵션을 사용하게 되면 부모에서 자식객체를 삭제하면 자동으로 삭제된다.
- 고아객체 (orphan) 제거: orphanRemoval = true
    - 고아객체란 연관관계가 끊어진 객체를 말한다. (참조가 제거된 엔티티)
    - 이 기능으로 부모엔티티의 컬렉션에서 자식 엔티티의 참조만 제거하면 자식엔티티가 자동으로 삭제된다.
    - 이 기능은 참조하는 곳이 하나일 때만 사용해야 하며 이런 이유로 orphanRemoval 옵션은 `OneToOne`과 `OneToMany`관계에만 사용이 가능하다.
    - 추가 기능으로 부모를 제거하면 자식이 고아가된다. 따라서 부모가 제거되면 자식도 제거된다. (= `CascadeType.REMOVE`)
   
## 다양한 CascadeType
- CascadeType.PERSIST
    - 엔티티를 생성하고, 연관 엔티티를 추가하였을 때 한 엔티티에서 persist()를 수행하면 연관 엔티티도 함께 persist()가 수행된다.  
    - 만약 연관 엔티티가 DB에 등록된 키값을 가지고 있다면 detached entity passed to persist Exception 이 발생한다.
- CascadeType.MERGE
    - 트랜잭션이 종료되고 detach 상태에서 연관 엔티티를 추가하거나 변경된 이후에 부모 엔티티가 merge()를 수행하게 되면 변경사항이 적용된다.
    - 연관 엔티티의 추가 및 수정 모두 반영됨
- CascadeType.REMOVE
    - 영속성 전이의 옵션중 하나로써 부모 엔티티를 삭제하면 자식엔티티도 삭제하는 기능.
    - 삭제 순서는 외래키 제약조건을 고려해서 자식을 먼저 삭제하고 부모를 삭제한다.
    - 만약 CascadeType.REMOVE 를 설정하지 않고 삭제한다면 부모엔티티에 대한 삭제를 요청하는 쿼리가 발생하는데,  
    - 데이터베이스의 외래키 제약 조건이 걸려있다면 데이터베이스의 외래키 무결성 예외가 발생한다.
- CascadeType.DETACH
    - 부모 엔티티가 detach()를 수행하게 되면, 연관된 엔티티도 detach() 상태가 되어 변경사항이 반영되지 않는다.
- CascadeType.ALL
    - 모든 Cascade 적용

## 참고  
- 자바 표준 ORM JPA 프로그래밍 (김영한)
- http://chomman.github.io/blog/java/jpa/programming/jpa-cascadetype-%EC%A2%85%EB%A5%98/
