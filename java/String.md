# String, StringBuffer, StringBuilder의 차이 

### **String**
**불변 객체**이기 때문에 문자열 연산이 많은 프로그래밍이 필요할 때 계속해서 인스턴스를 생성하므로 성능이 떨어지지만 조회가 많은 환경, 멀티쓰레드 환경에서 성능적으로 유리합니다.

###**StringBuffer와 StringBuilder**
문자열이 변경 가능한 가변객체이기 때문에 문자열 연산이 자주 발생할 때 성능적으로 유리합니다.  
StringBuffer와 StringBuilder의 **차이점은 동기화 지원의 유무**이고 동기화를 고려하지 않는 환경에서 StringBuilder가 성능이 더 좋고, 동기화가 필요한 멀티쓰레드 환경에서는 StringBuffer를 사용하는 것이 유리합니다.