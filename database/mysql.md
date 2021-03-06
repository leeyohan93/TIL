# MySQL 이해하기 + 최적화 (Index)

```sql
5) SELECT Country, COUNT(*) AS `고객 수` 
1) FROM Customers 
2)	WHERE Country <> 'Norway'
3)		GROUP BY Country
4)			HAVING COUNT(Country) = 1
6)				ORDER BY Country;

1. FROM에서 데이터 집합을 만듭니다. 여기서는 Customers 테이블의 데이터를 가져왔네요.
2. WHERE는 FROM에서 만든 데이터 집합을 조건에 맞게 걸러내는데요.
3. GROUP BY는 WHERE에서 필터링한 (조건에 맞는 데이터를 걸러낸) 데이터를 그룹화합니다.
4. HAVING은 GROUP BY에서 집계한 데이터 집합을 다시 조건에 맞게 필터링할 때 사용해요
5. SELECT에서는 그룹화하고 필터링한 데이터 집합을 집계합니다.
6. 모두 진행한 이후, ORDER BY를 통해 집계한 데이터 집합을 정렬합니다.
```

### 5.1 DB 최적화 대상

- 클라이언트
    - 호출을 줄인다, PreparedStatement(캐싱) , 커넥션 풀, batch size
- 데이터베이스엔진
    - 서버 파라미터를 튜닝합니다. (제일 마지막, 순정이 베스트)
- 파일시스템
    - SSD, **SQL 최적화 (읽어오는 블록 수를 줄인다)**

### 5.2 SQL 최적화 대상

### 5.2.1 블록단위 I/O

- 모든 DBMS에서 I/O는 블록 단위로 이루어진다. 블록 단위로 I/O한다는 것은 하나의 레코드에서 하나의 컬러만 읽으려 할 때도 레코드가 속한 블록 전체를 읽게됨을 뜻한다.
- Sequential 액세스: 레코드간 논리적 또는 물리적인 순서를 따라 차례대로 읽어 나가는 방식, 인덱스 리프 블록에 모든 레코드는 포인터를 따라 논리적으로 연결되어 있는데 이 포인터를 따라 스캔
- Random 액세스: 레코드간 논리적, 물리적인 순서를 따르지 않고 한건을 읽기 위해 한 블록씩 접근하는 방식이다.
- I/O 튜닝의 핵심은 Sequential 액세스의 선택도를 높이거나 Random 액세스 발생량을 낮추는 것

### 5.2.2 Single Block VS Multiblock I/O

- Single Block I/O 
한번의 I/O Call을 통해 하나의 데이터 블록만 읽어 메모리에 적재하는 것을 말한다.
e.g. index range scan /index full scan
- Multi block I/O
I/O Call이 필요한 시점에 인접한 블록들을 같이 읽어 메모리에 적재하는 것을 말한다. 
e.g. index fast full scan, table full scan
- 인덱스를 스캔할 때 Single Block I/O를 사용하는 이유
    - 인덱스 블록간의 논리적 순서는 물리적으로 데이터 파일에 저장된 순서와 다르다.
    인덱스 블록간 논리적 순서란, 인덱스 리프 블록끼리 이중 연결 리스트 구조로 연결된 순서를 말한다. 그래서, 물리적으로 한 Extent에 속한 블록들을 I/O Call 발생 시점에 같이 적재하여 올렸을 때, 그 블록들이 논리적 순서로는 한참 뒤쪽에 위치할 수 있으므로, 실제 사용되지 못한 채 버퍼상에서 밀려 날 수도 있으므로 Single Block I/O방식이 효율적이다.

### 5.2.3 인덱스와 테이블 스캔 방식

- Index란
    - 말 그대로 색인이며 DBMS 에서의 인덱스는 데이터의 저장 성능을 희생하고 그 대신 데이터의 읽기 속도를 높이는 기능이다.
    - 인덱스 탐색과정은 스캔 시작지점을 찾는 수직적 탐색과 데이터를 찾는 수평적 탐색, 두가지로 나눌 수 있으며, 수직적 탐색 후에 수평적 탐색을 한다
- Table Full Scan
    - 시퀀셜 액세스와 Multiblock I/O 방식으로 디스크를 읽어 한 블록에 속한 모든 레코드를 한번에 읽음
    - 파티션 활용 전략과 병렬 처리로 최적화가 가능하다
    - 읽을 데이터가 일정량을 넘으면 인덱스보다 Table Full Scan이 유리하다
- Index Range Scan
    - 랜덤 액세스와 Single Block I/O로 레코드 하나를 읽기 위해 매번 I/O가 발생
    - 소량 데이터를 주로 검색할때 효과적이다
    - 인덱스 컬럼을 가공하지 않아야 리프 블록에서 스캔 시작점을 찾아 거기서 부터 스캔하다가 중간에 멈출 수 있다
- Index Full Scan
    - index 컬럼을 사용한 카운트 쿼리 같은 경우
- Index Unique Scan
    - 인덱스가 존재하는 컬럼이 중복값이 입력되어 있지 않고 '='조건으로 찾을때 이 방식으로 처리된다

### 5.3 테이블 액세스 최소화하는 방법

- 인덱스 컬럼을 추가한다.
- Index Range Scan을 하기 위해서는 인덱스 선두 컬럼이 조건절에 있어야 한다
(여러 인덱스가 같이 있을 경우 순서에 따라 인덱스가 적용되기도 안되기도 하니 인덱스 순서를 주의해야한다)
- [NOT 비교, LIKE '%?', 인덱스 컬럼이 변경된 경우, 데이터 타입이 다른 경우]는 인덱스를 타지 못한다
- 인덱스는 항상 정렬 상태를 유지 하므로 인덱스 순서에 따라 OrderBy의 소트 연산을 생략할 수 있다
- [과세코드, 과세코드 + 이름, 과세코드 + 이름 + 연령]은 모두 중복이다. 마지막 과세코드 + 이름+ 연령을 사용하도록 하자
- 인덱스를 설계할때 꼭 기억하자
    - 조건절에 항상 사용하거나, 자주 사용 컬럼 선정한다
    - '=' 조건으로 자주조회하는 컬럼을 앞쪽에 둔다
    - Join문, 중첩쿼리 등을 사용할 경우 조건절을 활용하여 조회 범위를 줄이자
    
### 5.4 MySQL DB 엔진

- MySQL의 엔진인 InnoDB는 B+tree로 이뤄져있는데, 이는 B-tree의 확장된 개념이다.
- B-tree
    - B-tree의 핵심은 데이터가 정렬된 상태로 유지되어 있다는 것
    - 루트 노드, 브랜치 노드, 리프 노드
    - B-tree의 장점은 균일성으로 어떤 값에 대해서도 같은 시간에 결과를 얻을 수 있다.
- B+tree
    - B-tree의 경우, branch 노드에 key와 data를 담을 수 있다. 
    하지만,B+tree의 경우브랜치 노드에 key만 담아두고, data는 담지 않는다. 
    오직 리프 노드에만 key와 data를 저장하고, 리프 노드끼리 Linked list로 연결되어 있다.
    - 리프 노드를 제외하고 데이터를 담아두지 않기 때문에 메모리를 더 확보함으로써 더 많은 key들을 수용할 수 있다. 하나의 노드에 더 많은 key들을 담을 수 있기에 트리의 높이는 더 낮아진다.(cache hit를 높일 수 있음)
    - 풀 스캔 시, B+tree는 리프 노드에 데이터가 모두 있기 때문에 한 번의 선형탐색만 하면 되기 때문에 B-tree에 비해 빠르다. B-tree의 경우에는 모든 노드를 확인해야 한다.
    
## 참고
- 우아한테크캠프 pro - NEXTSTEP
