# 부모 객체에서 자식 객체를 관리하고 싶어요. (feat.DDD)

## CascadeType.REMOVE 를 등록하면 부모 객체에서 자식 객체를 지웠을때 삭제되도록 반영 되겠지 ?
- 결론부터 말하자면 그렇지 않다. CascadeType.REMOVE 는 부모에서 자식객체를 관리하는 영속성 전이 타입이 아니다.
- CascadeType.REMOVE
    - 영속성 전이의 옵션중 하나로써 부모 엔티티를 삭제하면 자식엔티티도 삭제하는 기능.
    - 삭제 순서는 외래키 제약조건을 고려해서 자식을 먼저 삭제하고 부모를 삭제한다.
    - 만약 CascadeType.REMOVE 를 설정하지 않고 삭제한다면 부모엔티티에 대한 삭제를 요청하는 쿼리가 발생하는데,  
    데이터베이스의 외래키 제약 조건이 걸려있다면 데이터베이스의 외래키 무결성 예외가 발생한다.
    
## 그렇다면 어떻게 부모 객체에서 자식 객체를 지울 수 있을까 ?
- JPA에서는 고아객체(연관관계가 끊어진 객체)를 다룰 수 있는 옵션이 있는데 이 옵션을 사용하게 되면 부모에서 자식객체를 삭제하면 자동으로 삭제된다.
- 고아객체 (orphan) 제거: orphanRemoval = true
    - 고아객체란 연관관계가 끊어진 객체를 말한다. (참조가 제거된 엔티티)
    - 이 기능으로 부모엔티티의 컬렉션에서 자식 엔티티의 참조만 제거하면 자식엔티티가 자동으로 삭제된다.
    - 이 기능은 참조하는 곳이 하나일 때만 사용해야 하며 이런 이유로 orphanRemoval 옵션은 OneToOne과 OneToMany에만 사용이 가능하다.
    - 추가 기능으로 부모를 제거하면 자식이 고아가된다. 따라서 부모가 제거되면 자식도 제거된다. (=CascadeType.REMOVE)
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


## 부모 객체에서 자식 객체를 관리하고 싶어요.
- CascadeType.ALL 과 orphanRemoval = true 를 같이 사용하자.
- 두 옵션을 모두 활성화하면 부모 엔티티에서 자식의 생명주기를 관리할 수 있다.
    - 자식을 저장하려면 부모에 등록만 하면 된다(CASCADE).
    - 자식을 삭제하려면 부모에서 제거하면 된다(orphanRemoval).
   
## 다른 영속성 전이에 대해서도 알아보자.
- CascadeType.PERSIST : 엔티티를 생성하고, 연관 엔티티를 추가하였을 때 persist() 를 수행하면 연관 엔티티도 함께 persist()가 수행된다.  
만약 연관 엔티티가 DB에 등록된 키값을 가지고 있다면 detached entity passed to persist Exception이 발생한다.
- CascadeType.MERGE : 트랜잭션이 종료되고 detach 상태에서 연관 엔티티를 추가하거나 변경된 이후에 부모 엔티티가 merge()를 수행하게 되면 변경사항이 적용된다.  
(연관 엔티티의 추가 및 수정 모두 반영됨)
- CascadeType.REMOVE – 삭제 시 연관된 엔티티도 같이 삭제됨
- CascadeType.DETACH – 부모 엔티티가 detach()를 수행하게 되면, 연관된 엔티티도 detach() 상태가 되어 변경사항이 반영되지 않는다.
- CascadeType.ALL – 모든 Cascade 적용

## 참고  
- 자바 표준 ORM JPA 프로그래밍 (김영한)
- http://chomman.github.io/blog/java/jpa/programming/jpa-cascadetype-%EC%A2%85%EB%A5%98/
