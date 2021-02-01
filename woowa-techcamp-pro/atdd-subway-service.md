# 인수테스트 기반 TDD

- [Repository](https://github.com/leeyohan93/atdd-subway-service)
- [Step1 PullRequest](https://github.com/next-step/atdd-subway-service/pull/12)
- [Step2 PullRequest](https://github.com/next-step/atdd-subway-service/pull/17)
- [Step3 PullRequest](https://github.com/next-step/atdd-subway-service/pull/27)
- [Step4 PullRequest](https://github.com/next-step/atdd-subway-service/pull/43)

## 피드백 모음

### 다른 애그리거트를 참조하는 것은 문제가 아니다. 중요한 것은 도메인 룰에 대한 책임을 적절한 애그리거트가 가지는 것이다.
![](./image/atdd-subway-service/다른서비스의_참조.png)

### 엔티티 작성 시 컬럼의 명확한 매핑을 위한 어노테이션을 사용하라
![](./image/atdd-subway-service/엔티티를_작성규칙.png)

### 인프라 레이어에는 외부 API 나 라이브러리와 같은 외부 의존성을 가질때 사용한다.
![](./image/atdd-subway-service/인프라레이어의_구현체들.png) 

### 제네릭을 사용하라
![](./image/atdd-subway-service/제네릭_사용.png)

### 테스트가 복잡하다면 주석을 활용하라
![](./image/atdd-subway-service/테스트_주석.png)

### 사이드 이펙트를 항상 조심하라
![](./image/atdd-subway-service/사이드이펙트.png)

### Cousumer 활용
![](./image/atdd-subway-service/Consumer_활용.png)

