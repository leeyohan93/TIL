# 모든 개발자를 위한 HTTP 웹 기본 지식

## 주요 내용 정리

### IP(인터넷 프로토콜)
- 역할 : 지정한 IP 주소에 데이터 전달, 패킷이라는 통신 단위로 데이터 전달
- 패킷 구성: 출발지 IP, 목적지 IP, 기타 정보
- 한계
    - 비연결성: 패킷을 받을 대상이 없거나 서비스 불능 상태여도 패킷 전송
    - 비신뢰성: 중간에 패킷이사라지면? 패킷이 순서대로 안오면(전달 순서 문제)?
    - 프로그램구분: 같은 IP를 사용하는 서버에서 통신하는 애플레케이션이 두개 이상이면?
- 이 문제를 해결해주는 것이 TCP

### TCP와 UDP
- 네트워크 정리
    - 복잡한 인터넷 망을 메시지를 보내기 위해 IP가 필요
    - IP만으로는 여러 문제가 있기 때문에 TCP가 도입
    - UDP는 거의 백지상태이고 필요시에 기능 확장으로 사용이 가능하다
    - PORT는 같은 IP 안에서 동작하는 통신하는 애플리케이션을 구분하기 위해서 사용
    - DNS는 IP가 외우기 어려워서 매핑해서 사용한다.
- 요청 흐름
    1. 메시지 생성
    2. 소켓 라이브러리를 통해 전달
    3. TCP 정보 생성, 메시지 데이터 포함
    4. IP 패킷 생성, TCP 데이터 포함
    5. 전송
- TCP
    - 구성: PORT, 전송 제어, 순서, 검증 정보, 데이터 등
    - 연결지향: 3 way handshake: SYN → SYN+ACK → ACK
        - 클라이언트와 서버를 먼저 연결하고 전송
        - 요즘은 최적화되서 ACK 시 데이터 전송
    - 데이터 전달 보증 → 데이터 전달 응답을 받는다
    - 순서 보장
    - 신뢰할 수 있는 프로토콜
- UDP
    - 하얀 도좌히 (기능이 거의 없음)
    - IP와 거의 같지만 PORT 및 체크섬 정도만 추가
    - 최근 HTTP 3스펙에서 최적화를 통한 UDP 프로토콜을 사용해서 주목받고 있다.
- PORT: IP가 아파트 PORT가 몇동 몇호
- DNS: 전화번호부, 도메인 이름을 IP와 매핑

### 웹 브라우저 요청 흐름
1. resource 요청 시,  DNS 서버를 통해 IP와 PORT를 조회
2. 웹 브라우저가 HTTP 메시지 생성
3. SOCKET 라이브러리를 통해 전달
    - A: TCP/IP로 3way handshake를 실행해 서버와 연결
    - B: 데이터 전달
4. TCP/IP 계층에서 HTTP 메시지가 포함된 TCP/IP 패킷을 생성한다.
5. 패킷 정보가 인터넷으로 흘러간다.
6. 서버에 요청  패킷이 도착하여 패킷 껍데기는 버리고 HTTP 메시지를 서버가 해석한다.
7. HTTP 응답 메시지를 마찬가지 방식으로 패킷을 생성하여 응답 패킷을 전달한다.
8. 수 많은 노드들을 통해서 응답 패킷이 도착하게 되면 웹 브라우저가 HTML 렌더링하여 화면에 보여준다.

### HTTP 특징
1. 클라이언트 서버 구조
    - 클라이언트는 서버에 요청을 보내고, 응답을 대기한다
    - 요청과 응답을 분리함으로써 서버와 클라이언트의 독립적인 진화가 가능하도록 한다.
    (비즈니스 로직은 서버에서!, 클라이언트는 서버를 어떻게 구현하는지 관심 X)
2. Stateful, Stateless
    - Stateful
        - 중간에 서버가 바뀌면 안된다. (스케일 아웃을 할 수 없다)
        - 중간에 서버 장애가 나면 처음부터 다시 해야한다.
    - Stateless
        - 스케일 아웃에 유리 (트래픽이 몰렸을때 서버 증설이 가능하다)
        - 데이터 전송량이 많아진다.
        - 모든 것을 무상태로 설계할 수 없는 한계가 있다.(로그인)
        그래도 상태유지는 최소한으로만 사용해야 한다.
3. 비연결성
    - HTTP는 기본이 연결을 유지하지 않는 모델
        - 서버 자원을 매우 효율적으로 사용할 수 있음
    - 일반적으로 초 단위의 이하의 빠른 속도로 응답
    - 1시간 동안 수천명이 서비스를 사용해도 실제 서버에서 동시에 처리하는 요청은 수십개 이하로 매우 작음
    - 예) 웹 브라우저에서 계속 연속해서 검색 버튼을 누르지는 않는다.
    - 단점: TCP/IP 연결을 새로 맺어야 함. 많은 자원을 다시 다운로드 받아야한다.
        - 지금은 HTTP의 지속연결로 해결하고 있다.
        - 한번 연결하면 몇십초까지 연결을 유지하는 전략
    - 같은 시간에 딱 맞추어 발생하는 대용량 트래픽
        - 최대한 머리를 쥐어짜서 비연결성으로 짜야 한다.
        아무 상태가없는 HTML이벤트 페이지를 제공해서 놀게하거나 해서 푸는 등 여러 방법을 동원하자!

### HTTP 캐시
- 기본 동작
    1. 최초 요청의 응답에 캐시 정보(cache-control)를 보고 응답 결과를 브라우저 캐시에 저장한다.
    2. 두 번째 요청 시 캐시를 먼저 확인한다.
    3. 세 번째 요청(캐시 시간 초과) 응답 결과를 받아와서 다시 캐시에 저장
    - 데이터가 변하지 않았다면 다시 응답 결과를 받는 것은 낭비이다.
    그래서 조건부 요청 헤더를 이용하여 304 응답 시에는 캐시 시간을 갱신해서 다시 캐시를 사용한다.
- 캐시 제어 헤더
    - Cache-Control: max-age
        - 캐시 유효시간, 초 단위
    - Cache-Control: no-cache
        - 데이터는 캐시해도 되지만, 항상 원(origin) 서버에 검증하고 사용
    - Cache-Control: no-store
        - 데이터에 민감한 정보가 있으므로 저장하면 안됨
        (메모리에서 사용하고 최대한 빨리 삭제)
    - Pragma: no-cache
        - HTTP 1.0 하위 호환
    - expires: Mon, 01 Jan 1990 00:00:00 GMT
        - 캐시 만료일을 정확한 날짜로 지정, 지금은 더 유연한 Cache-Control: max-age 권장
        - Cache-Control: max-age와 함께 사용하면 Expires는 무시 (하위호환)
- 조건부 요청 헤더
    - Last-Modified, If-Modified-Since
        - 단점: 1초 미만(0.x초) 단위로 캐시 조정이 불가능
        데이터를 수정해서 날짜가 다르지만, 같은 데이터를 수정해서 데이터 결과가 똑같은 경우, 서버에서 별도의 캐시 로직을 관리하고 싶은 경우
    - ETag, If-None-Match
        - 진짜 단순하게 ETag만 서버에 보내서 같으면 유지, 다르면 다시 받기,
        **캐시 제어 로직을 서버에서 완전히 관리**
- 프록시 캐시
- 캐시 무효화
    - 저장하기 민감한 정보는 캐시하면 위험하므로 메모리에 저장해서 최대한 짧게 캐시하거나 하지 않는다.
    - Cache-Control: no-cache, no-store, must-revalidate, Pragma: no-cache
        
## 인상적인 질문 모음
- [POST vs PATCH](https://www.inflearn.com/questions/110170)
- [PATCH 메서드가 멱등이 아닌 이유](https://www.inflearn.com/questions/110644)
- [HTTP 지속연결과 keep-alive](https://www.inflearn.com/questions/110693)
- [봉투 패턴(Envelope pattern)](https://www.inflearn.com/questions/111465)
- [캐시 무효화에 여러 옵션을 추가해야하는 이유](https://www.inflearn.com/questions/112647)
- [RESTful API](https://www.inflearn.com/questions/126743)

## 강의 링크
- [모든 개발자를 위한 HTTP 웹 기본 지식 - 김영한 님](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC)
