# JPA 환경에서 일급 컬렉션 사용기

## 1. 일급 컬렉션이란 ?

일급 컬렉션이란 단어는 [소트웍스 앤솔로지](https://coupa.ng/bgRZDf) 의 **객체지향 생활체조** 파트에서 언급이 되었습니다.

> 일급 콜렉션 사용이 규칙의 적용은 간단하다.콜렉션을 포함한 클래스는 반드시 다른 멤버 변수가 없어야 한다.각 콜렉션은 그 자체로 포장돼 있으므로 이제 콜렉션과 관련된 동작은 근거지가 마련된셈이다.필터가 이 새 클래스의 일부가 됨을 알 수 있다.필터는 또한 스스로 함수 객체가 될 수 있다.또한 새 클래스는 두 그룹을 같이 묶는다든가 그룹의 각 원소에 규칙을 적용하는 등의 동작을 처리할 수 있다.이는 인스턴스 변수에 대한 규칙의 확실한 확장이지만 그 자체를 위해서도 중요하다.콜렉션은 실로 매우 유용한 원시 타입이다.많은 동작이 있지만 후임 프로그래머나 유지보수 담당자에 의미적 의도나 단초는 거의 없다. - 소트웍스 앤솔로지 객체지향 생활체조편

일급 컬렉션을 사용하게 되면 아래와 같은 장점을 얻을 수 있습니다.
1. 비지니스에 종속적인 자료구조
2. Collection 불변성 보장
3. 상태와 행위를 한 곳에서 관리
4. 이름이 있는 컬렉션

### 1-1. 일급 컬렉션 예제 코드

일급 컬렉션이라고 어려운 것이 아니며 하나의 컬렉션을 포장하여 처리하는 것입니다.  

아래는 간단한 예제코드로 일급 컬렉션을 활용한 **로또 복권 클래스**입니다.

```java
public class LottoTicket { 
    public static final int LOTTO_NUMBER_COUNT = 6;
    private final List<Long> lottoNumbers;
    
    public LottoTicket(List<Long> lottoNumbers) { 
        this.lottoNumbers = Collections.unmodifiableList(lottoNumbers); 
    }

    public void validateSize(List<Long> lottoNumbers) { 
        if (lottoNumbers.size() != LOTTO_NUMBER_COUNT) { 
            throw new IllegalArgumentException(); 
        } 
    }
    
    public void validateDuplicatedNumber(List<Long> lottoNumbers) { 
        Set<Long> removedDuplication = new HashSet<>(lottoNumbers);
        if (removedDuplication.size() != LOTTO_NUMBER_COUNT) { 
            throw new IllegalArgumentException(); 
        } 
    }
}

```

예제 코드를 가지고 앞서 이야기했던 일급 컬렉션의 장점에 대해 이야기해보겠습니다.

### 1. 비지니스에 종속적인 자료구조

LottoTiket이라는 일급 컬렉션을 만들면서 아래에 대한 비즈니스 조건들을 만족하는 모델이 도출되었습니다.

- 로또 티켓의 로또 번호는 6개이다.
- 로또 번호들은 중복될 수 없다.

### 2. Collection의 불변성을 보장

일반적인 불변 객체뿐만 아니라 Collection 또한 불변하게 만든다면 안정적인 구조로 만들어 갈 수 있습니다. 
아래는 일급 컬렉션에서 불변성을 보장하기 위해 사용한 부분들입니다.

- final 선언으로 참조 재할당 불가
- Collections.unmodifiableList()로 List에 add(), remove() 불가
- 내부의 lottoNumbers는 반환이 필요할 경우 방어적 복사를 통해 불변을 보장해야 합니다. 
  방어적 복사를 하는 이유는 컬렉션에 final을 선언하여도 컬렉션 내부의 객체에 대한 상태 변경이 가능하기 때문에 불변을 보장하기 위해서입니다.

```java
public List<Long> getLottoNumbers() {
    return new ArrayList(lottoNumbers); // 방어적 복사
}
```

### 3. 상태와 행위를 한 곳에서 관리

로또 티켓에 관한 아래의 기능을 로또 티켓 클래스 안에서 구현이 가능합니다.

- 로또 티켓에 관련된 validation
- 로또 티켓의 당첨 여부

### 4. 이름이 있는 컬렉션

컬렉션에 대한 적절한 이름(**LottoTicket**)이 주어지는 것은 생각보다 더 많이 강력한 일입니다.


## 2. JPA에서 일급 컬렉션을 어떻게 사용할 수 있을까

**일급 컬렉션 반영 전**

```java
@Entity
public class Team { 
    private static final int MEMBER_LIMIT = 5;

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @OneToMany(mappedBy = "team")
    private List<Member> members = new ArrayList<>();
    
    public void setMembers(List<Member> members) { 
        validateMemberCount(members);
        this.members.addAll(members);
    }
    
    private void validateMemberCount(List<Member> members) { 
        if (members.size() > MEMBER_LIMIT) { 
            throw new IllegalArgumentException(); 
        }
    }
}

@Entity
public class Member {
    
    @Id 
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @ManyToOne
    @JoinColumn(name = "team_id")
    private Team team;
}
```

일급 컬렉션을 반영하기 이전의 코드에서는 팀에서 회원의 인원 제한과 같은 검증을 진행하고 있습니다.
해당 방법도 나쁘지는 않지만 일급 컬렉션을 반영해보겠습니다.

**일급 컬렉션 반영 후**

```java
@Entity
public class Team {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Embedded
    private Members members;
    
    public Team() { 
        members = new Members();
    }
    
    public void setMembers(List<Member> members) { 
        this.members.setMembers(members);
    }
}

@Embeddable
public class Members {
    private static final int MEMBER_LIMIT = 5;
    
    @OneToMany(mappedBy = "team")
    private List<Member> members = new ArrayList<>();
    
    public Members() {
    }
    
    public void setMembers(List<Member> members) { 
        validateMemberCount(members);
        this.members.addAll(members);
    }
    
    private void validateMemberCount(List<Member> members) { 
        if (members.size() > MEMBER_LIMIT) { 
            throw new IllegalArgumentException(); 
        }
    }
}

@Entity
public class Member {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @ManyToOne
    @JoinColumn(name = "team_id")
    private Team team;
}
```

임베디드 타입을 사용하여 생각보다 쉽고 자연스럽게 일급 컬렉션이 연관관계 속에 녹아들었습니다.
이제는 회원의 일급 컬렉션에서 회원의 인원 제한에 대한 검증을 하기 때문에 검증 로직이 좀 더 알맞은 책임을 가진 클래스에 반영되게 되었습니다.
그 외에도 일급 컬렉션을 사용하게 됨으로 써 앞서 이야기하였던 여러 일급 컬렉션의 장점들을 가지고 올 수 있게 되었습니다.

## 3. 주의사항

### 1. 임베디드 타입의 전역 상수화 시 생기는 문제점
임베디드 타입의 경우 불변 객체로 만들 수 없기 때문에 전역 상수화하여 임베디드 타입을 공유하게 된다면 문제가 될 수 있습니다. 
이에 대해서는 [동욱님 블로그](https://jojoldu.tistory.com/559?category=637935)에 잘 설명되어 있습니다.

### 2. 임베디드 타입과 null의 관계, 1차 캐시
임베디드 타입에는 null과 관련된 특징이 있습니다.
1. 임베디드 타입이 null이면 매핑한 컬럼 값은 모두 null 이다.
2. 데이터베이스에서 조회 시 매핑한 컬럼이 모두 null이라면 임베디드 타입 또한 null 이다.

그렇다면 데이터베이스에서 한 명의 **Member**도 속하지 않은 **Team**을 조회하였을때 members 필드는 null 일까요? 정답은 null이 아니다입니다. 
그 이유는 Members.members 필드를 하이버네이트가 PersistenceBag이라는 List의 래퍼 컬렉션으로 초기화하기 때문입니다.

그렇다면 임베디드 타입 객체의 생성은 하이버네이트에게 맡기면 될까요? 그렇지 않습니다. 
만약 Team 클래스를 생성할 때 임베디드 타입을 생성하지 않는다면 다음과 같은 상황이 올 수 있습니다.

```java
@Test
void 임베디드타입_생성을_하이버네이트에게_맡기면_null로_초기화된다 () {
    Team team = new Team(); // 임베디드 타입을 초기화 하지 않음.
    Team savedTeam=TeamRepository.save(team);
    TeamRepository.findById(savedTeam.getId());

    assertThat(savedTeam.getMembers()).isNull();
}
```

위 테스트는 실패하지 않고 정상적으로 동작합니다.
분명 데이터베이스에서 조회하면 team.members의 내부는 PersistenceBag으로 초기화되기 때문에 null이 아니어야 하는데 왜 null일까요? 
그 이유는 JPA 1차 캐시에 있습니다. findById()와 같은 식별자를 통한 조회와 JPQL을 통한 조회는 1차 캐시에 이미 엔티티가 존재한다면 1차 캐시의 객체를 반환하게 되기 때문입니다. 

이에 대한 자세한 설명은 [JPA 1차 캐시 포스팅](https://github.com/leeyohan93/TIL/blob/master/jpa/JPA1%EC%B0%A8%EC%BA%90%EC%8B%9C.md) 에 되어 있습니다.


**따라서 임베디드 타입으로 일급 컬렉션을 사용하실 때는 반드시 임베디드 타입을 new 키워드로 매번 생성해주셔야 합니다.**.

## 4. 결론
이렇게 일급 컬렉션을 실무에서 사용하기 위해 JPA에 적용해 보았습니다. 생각보다 장점이 많고 객체지향적으로 코드를 작성할 수 있도록 도와주기 때문에 한번 시도해보시면 좋을 것 같습니다.

### 참고

[https://jojoldu.tistory.com/412](https://jojoldu.tistory.com/412)

[https://jojoldu.tistory.com/559?category=637935](https://jojoldu.tistory.com/559?category=637935)