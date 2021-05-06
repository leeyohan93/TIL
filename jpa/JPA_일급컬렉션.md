# JPA에서 일급 컬렉션 사용하기

### 1. 일급 컬렉션이란 ?

일급 컬렉션이란 단어는 [소트웍스 앤솔로지](https://coupa.ng/bgRZDf) 의 **객체지향 생활체조** 파트에서 언급이 되었습니다.

> 규칙 8: 일급 콜렉션 사용이 규칙의 적용은 간단하다.콜렉션을 포함한 클래스는 반드시 다른 멤버 변수가 없어야 한다.각 콜렉션은 그 자체로 포장돼 있으므로 이제 콜렉션과 관련된 동작은 근거지가 마련된셈이다.필터가 이 새 클래스의 일부가 됨을 알 수 있다.필터는 또한 스스로 함수 객체가 될 수 있다.또한 새 클래스는 두 그룹을 같이 묶는다든가 그룹의 각 원소에 규칙을 적용하는 등의 동작을 처리할 수 있다.이는 인스턴스 변수에 대한 규칙의 확실한 확장이지만 그 자체를 위해서도 중요하다.콜렉션은 실로 매우 유용한 원시 타입이다.많은 동작이 있지만 후임 프로그래머나 유지보수 담당자에 의미적 의도나 단초는 거의 없다. - 소트웍스 앤솔로지 객체지향 생활체조편

간단하게 설명하면 아래와 같은 **로또 복권 클래스**를 만드는 것이 일급 컬렉션을 만들어 사용하는 것이라고 볼 수 있습니다.

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

이렇게 만들어진 일급 컬렉션은 아래와 같은 장점을 가집니다.

1. 비지니스에 종속적인 자료구조
    - 로또 티켓의 로또 번호는 6개이다.
    - 로또 번호들은 중복될 수 없다.
2. Collection의 불변성을 보장
    - final로 재할당 불가
    - Collections.unmodifiableList()로 add, remove 불가
    - 내부의 lottoNumbers는 반환이 필요할 경우 방어적 복사를 통해 불변을 보장
3. 상태와 행위를 한 곳에서 관리
    - 로또 티켓에 관한 기능들을 로또 티켓 클래스 안에서 구현 가능 (당첨번호 비교 등)
4. 이름이 있는 컬렉션
    - LottoTicket

일급 컬렉션에 대한 다른 자세한 설명은 [이동욱님 블로그](https://jojoldu.tistory.com/412)에 잘 정리가 되어 있습니다.

### 2. JPA에서 일급 컬렉션을 어떻게 사용할 수 있을까

일급 컬렉션 좋은 건 알겠는데, 실무에는 어떻게 적용할 수 있을까.

### 3. 일급 컬렉션 사용 시 주의사항

### 참고

[https://jojoldu.tistory.com/412](https://jojoldu.tistory.com/412)