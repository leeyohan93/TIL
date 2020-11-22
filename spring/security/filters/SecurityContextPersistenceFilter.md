# SecurityContextPersistenceFilter

## SecurityContextPersistenceFilter 란?
- 여러 요청 간에 SecurityContext를 공유할 수 있도록 해주는 필터.
- 이미 인증을 마친 SecurityContext를 공유함으로 써 새로운 요청 시 인증 절차를 거치지 않도록 해준다. 
- 인증과 관련된 여러 필터들 보다 앞에 위치한다. (인증받은 SecurityContext가 재 인증을 거치지 않게 하기 위함.)
- 기본적으로 사용하는 전략은 HTTP Session 에서 읽어오는 전략이다.
