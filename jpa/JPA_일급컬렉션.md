# JPA 엔티티에서 일급 컬렉션 사용해보기

## 1\. 일급 컬렉션이란?

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

    public int match(final LottoTicket winningLottoTicket) {
        return (int) lottoNumbers.stream()
                .filter(winningLottoTicket::contains)
                .count();
    }
}
```

예제 코드는 일급 컬렉션을 활용한 **로또 복권 클래스**입니다.

일급 컬렉션이라고 어려운 것이 아니며 하나의 컬렉션을 포장하여 처리하는 것입니다.  
일급 컬렉션을 사용하게 되면 아래와 같은 장점을 얻을 수 있습니다.

1.  비즈니스에 종속적인 자료구조 - 6개의 로또 번호를 가지는 로또티켓 클래스
2.  Collection 불변성 보장 - final을 통한 불변성 보장
3.  상태와 행위를 한 곳에서 관리 - lottoNumbers 와 match()를 한 곳에서 관리
4.  이름이 있는 컬렉션 - 로또티켓이라는 클래스 명

더 자세한 설명은 [향로님의 일급 컬렉션의 소개와 써야할 이유](https://jojoldu.tistory.com/412) 포스팅을 참고하시면 좋습니다.

## **2\. JPA 엔티티 연관관계에서 일급 컬렉션 사용해보기**

JPA 엔티티 연관관계에서는 **임베디드 타입을 사용**하면 손 쉽게 일급 컬렉션을 반영할 수 있습니다.

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

일급 컬렉션을 반영하기 이전의 코드에서는 팀에서 회원의 인원 제한과 같은 검증 행위를 진행하고 있습니다.  
이제 일급 컬렉션을 반영해보겠습니다.

**일급 컬렉션 반영**

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

예제처럼 임베디드 타입을 사용하면 생각보다 쉽게 일급 컬렉션을 JPA 엔티티 연관관계에 반영할 수 있습니다.  
일급 컬렉션을 통해 비즈니스에 종속적인 5명 제한의 Members 클래스를 만들고 이후에 members와 관련된 행위들이 일급 컬렉션에 추가될 수 있습니다.  
다만 JPA에서는 필드를 프록시로 사용하기 때문에 final을 통한 컬렉션의 불변성 보장은 할 수 없으니 주의해주셔야 합니다.

실제로 일급 컬렉션을 사용해오면서 느낀 큰 장점은 작은 단위의 테스트를 진행할 수 있다는 점입니다.  
일급 컬렉션 반영 이전에는 회원의 **회원의 인원 제한에 대한 테스트**를 진행하려면 Team 클래스에서 테스트를 구현했어야 했습니다.  
하지만 일급 컬렉션 Members 클래스를 반영하니 Members 클래스에서 테스트를 구현할 수 있었고 그 외에도 Team 클래스에 모든 테스트가 구현되는 현상을 막을 수 있었습니다.

## 3\. 주의사항

임베디드 타입에는 null과 관련된 특징이 있습니다.

1.  임베디드 타입이 null이면 매핑된 컬럼 값은 모두 null이다.
2.  데이터베이스에서 조회 시 매핑된 컬럼이 모두 null이라면 임베디드 타입 또한 null이다.

그렇다면 데이터베이스에서 한 명의 **Member**도 속하지 않은 **Team**을 조회하였을 때 members 필드는 null일까요? 정답은 null 이 아니다입니다.  
그 이유는 Members.members 필드를 JPA 가 프록시로 초기화하기 때문입니다.

그렇다면 임베디드 타입 객체의 생성은 JPA에게 맡기면 될까요? 그렇지 않습니다.  
만약 Team 클래스를 생성할 때 임베디드 타입을 생성하지 않는다면 다음과 같은 상황이 올 수 있습니다.

```java
@Test
void 임베디드타입_생성을_하이버네이트에게_맡기면_null로_초기화되지_않는다 () {
    Team team = new Team(); // 임베디드 타입을 초기화 하지 않음.
    Team savedTeam=TeamRepository.save(team);
    TeamRepository.findById(savedTeam.getId());

    assertThat(savedTeam.getMembers()).isNotNull();
}
```

위 테스트의 실행 결과는 실패입니다. 데이터베이스에서 조회하면 team.members의 내부는 프록시로 초기화되기 때문에 null이 아니어하는 것이 아닌가요?  
그 이유는 JPA 1차 캐시에 있습니다. findById()와 같은 식별자를 통한 조회와 JPQL을 통한 조회는 1차 캐시에 이미 엔티티가 존재한다면 1차 캐시의 객체를 반환하게 되기 때문입니다.

이에 대한 자세한 설명은 [JPA 1차 캐시 포스팅에](https://github.com/leeyohan93/TIL/blob/master/jpa/JPA1%EC%B0%A8%EC%BA%90%EC%8B%9C.md) 되어 있습니다.

**따라서 임베디드 타입으로 일급 컬렉션을 사용하실 때는 반드시 임베디드 타입을 new 키워드로 매번 생성해주셔야 합니다.**

## 4\. 결론

JPA 연관관계에서 일급 컬렉션을 사용하면 장점이 많고 객체지향적으로 코드를 작성할 수 있도록 도와주기 때문에 한번 시도해보시면 좋을 것 같습니다.

### 참고

[https://jojoldu.tistory.com/412](https://jojoldu.tistory.com/412)

[https://jojoldu.tistory.com/559?category=637935](https://jojoldu.tistory.com/559?category=637935)