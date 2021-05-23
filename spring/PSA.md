# 스프링 PSA

## 스프링에서 PSA란?
하나의 추상화로 여러 서비스를 묶어둔 것을 Portable Service Abstraction라 합니다.

## PSA의 예시
대표적인 PSA로는 스프링의 트랜잭션이 있습니다.  
데이터베이스에 접근하는 방법은 JDBC, JPA 등 여러 방법이 존재하는데 신기하게도 어떠한 경우라도 트랜잭션 어노테이션을 이용하면 트랜잭션 기능을 추가할 수 있습니다.
그 이유는 트랜잭션 로직을 처리할 때 각 접근하는 기술 스택에 맞는 `TransactionManager`를  구현하고 있는 것이 아니라 추상화된 `PlatformTransactionManager`를 의존하여
필요한 `TransactionManager`를 DI로 주입받아 사용함으로써 하나의 기술에 국한되지 않고 사용이 가능하게 됩니다.