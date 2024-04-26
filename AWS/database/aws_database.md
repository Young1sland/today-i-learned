## AWS Database
|Type|Service|Description|Usage|
|--|--|--|--|
|관계형|RDS, Amazon Aurora,Amazon Redshift|데이터의 관계에 집중, 트랜잭션 지원|일반적인 어플, 온라인 게임 등|
|키-값|DynamoDB|파티셔닝이 가능. 다른 유형의 DB는 불가능한 범위까지 수평확장 가능|세션 저장, 장바구니|
|문서|DocumentDB|데이터를 Json 혹은 유사 형식의 문서로 데이터를 저장,쿼리. Nest된 구조로 문서 저장 가능. DocumentDB는 MongoDB와 호환됨|컨텐츠 관리, 카탈로그|
|인메모리|ElasticCache, MemoryDB For Redis|메모리에 저장.매우 빠름. 내구성 떨어짐|실시간 경매, 게임 랭킹보드, 캐싱|
|그래프|Neptune|데이터보다 데이터간 관계에 줌시을 둔 DB|소셜 네트워크, 이상탐지(다른 패턴 감지), 추천 엔진(성향 감지)|
|타임시리즈|Timestream|많은 이벤트를 시간단위로 저장. 수십만건의 데이터를 시간에 따라 정렬하고 특정 시점의 이벤트를 쿼리. 많은 IO|IOT기기 이벤트 관리. 분석 어플리케이션|
|원장(Ledger),장부|QLDB(Quantum Ledger Database)|신뢰성,투명성. 변경내역, 무결성 확보에 중점. 블록체인 네트워크 및 암호화를 통해 무결성 확보|금융거래 기록|
|검색|Elasticsearch Service|인덱싱과 카테고리 기능에 특화. 빠른 검색|컨텐츠 검색, 로그분석|

### 관계형 데이터베이스
- OLTP(Online Transactional Processing)
  - 주로 데이터의 트랜잭션을 다루는 데이터베이스.
  - 예를 들어 id에 해당되는 유저의 이름,나이를 조회 혹은 수정. 주로 행을 기준으로 조회
  - AWS 서비스: Amazon RDS
- OLAP(Online analytical processing)
  - 데이터를 종합적으로 보고 통계를 산출하는데 특화된 데이터베이스
  - 예를 들어 한달동안 서울에서 가장 많이 팔린 인형 종류를 조회. 주로 열을 기준으로 조회
  - 주로 데이터 웨어하우스에서 처리
  - AWS 서비스: Amazon Redshift


### DynamoDB 항목 예시
|파티션키|정렬키|기타속성|
|--|--|--|
|제품 id|타입|이름, 작가, 가수, 출시일(속성은 자유롭게 변경 가능)|

### Amazon MemoryDB for Redis
내구성을 확보한 In-memory DB로 메인 DB로 사용할 수 있게 만듦.

## Amazon RDS 백업
- 자동 백업
  - 매일마다 스냅샵을 만들고 트랜잭션 로그를 저장.
  - 데이터는 S3에 저장되며 데이터베이스의 크기만큼의 공간 점유
  - 저장된 데이터를 바탕으로 일정 기간 내의 특정 시간으로 롤백 가능
    - 롤백은 다른 DB 클러스터를 새로 생성하는 형태이다. Aurora는 해당 DB도 롤백 가능하다.
  - 1~35일까지 보관 지원
  - Backup을 시행할 때는 약간의 딜레이 발생 가능성
  - 기본적으로 사용 상태로 설정되어 있음
- 수동 백업(DB 스냅샷)
  - 유저, 혹은 다른 프로세스로 부터 요청에 따라 만들어지는 스냅샷
  - 데이터베이스가 삭제된 이후에도 계속 보관. 스냅샷의 복구는 향상 새로운 DB Instance를 생성하여 수행

## RDS 클러스터 구성
||Multi-AZ|Multi-Region|Read Replica|
|--|--|--|--|
|목적|고가용성|DR/local performance|확장성/성능|
|복제방식|Sync|Async(딜레이 있을 수 있음)|Async(딜레이 있을 수 있음)|
|액티브|Primary DB만 읽기/쓰기 가능|Read만 가능|Read만 가능|
|백업|자동백업(Standby 기준으로 스냅샷)|자동 백업 가능|디폴트로 백업안함. 설정은 가능|
|엔진 업데이트|Primary만 업데이트|각 리전별로 다른 업데이트|DB별로 다른 엡데이트|
|FailOver|자동으로 StandBy로 Failover|수동으로 FailOver|수동으로 FailOver|


### Multi AZ
- 두개 이상의 AZ에 걸쳐 데이터베이스를 구축하고 원본과 다른 DB(StandBy)를 자동으로 동기화(Sync)
  - SQL Server, Oracle, MySql, PostgreSQL, MariaDB에서 지원
  - Aurora의 경우 다중 AZ를 설계 단계에서 지원
- StandBy DB는 접근 불가능, 퍼포먼스 상승 효과가 아닌 안정성을 위한 서비스
- 원본 DB 장애 발생 시 자동으로 다른 DB가 원본으로 승격됨(DNS가 Standby DB로)
  - DB 생성 시 엔드포인트는 Primary DB를 가리키고 있으며 장애가 발생하면 Standby DB로 자동으로 변경 된다.
    ```sh
    $ while true; do host my-db.XXX.ap-northeast-2.rds.amazonaws.com | grep alias ; sleep 1; done
        my-db.XXX.ap-northeast-2.rds.amazonaws.com is an alias for ec2-X-XX-123-01.ap-northeast-2.compute.amazonaws.com.
        my-db.XXX.ap-northeast-2.rds.amazonaws.com is an alias for ec2-X-XX-123-01.ap-northeast-2.compute.amazonaws.com.
        -> 데이터베이스 Reboot, 장애 조치로 재부팅 선택하면 장애 테스트 가능. 자동으로 Standby 인스턴스로 변경 됨.
        my-db.XXX.ap-northeast-2.rds.amazonaws.com is an alias for ec2-X-XX-81-03.ap-northeast-2.compute.amazonaws.com.
        my-db.XXX.ap-northeast-2.rds.amazonaws.com is an alias for ec2-X-XX-81-03.ap-northeast-2.compute.amazonaws.com.        
    ```

### 읽기 전용 복제본
- 원래 DB의 일기 전용 복제본을 생성(Sync)
  - 쓰기는 원본 데이터베이스에, 읽기는 복제본에서 처리하여 워크로드 분산
  - MySql, PostgreSQL, MariaDB, Oracle, Aurora에서 지원
- 안정성이 아닌 퍼포먼스를 위한 서비스
- 총 5개까지 생성 가능
- 각각의 복제본은 고유 DNS가 할당되어 접근 가능
  - 원본 DB 장애 발생 시 수동으로 DNS 변경이 필요함
- 복제본 자체에 Multi-AZ 설정 가능(MySQL, MariaDB, PostgreSQL, Oracle)
- Multi-AZ DB에 Read Replica 설정 가능
- 자동 백업이 활성화 되어 있어야 읽기 전용 복제본 생성 가능
- 각 DB의 엔진 버전이 다를 수 있음

### Multi Region
- 다른 리전에 지속적으로 동기화 시키는 DB 클러스터를 생성
  - Async 복제
- 주로 로컬 퍼포먼스 혹은 DR(disaster recovery) 시나리오로 활용
- 각 리전별로 자동 백업 가능
- 리전별로 Multi-AZ 가능

## RDS 구성 설정
- parameter 그룹은 DB의 종류(MySQL, Aurora 등)에 따라 적용되는 것. 옵션 그룹은 개별의 인스턴스에 적용되는 것.


## Amazon Aurora
- 고성능 상용 데이터베이스의 성능과 가용성에 오픈 소스 DB의 간편성과 비용 효율성을 결합. Mysql과 PostgreSQL 호환.
- MySQL에 비해 최대 5배 빠르고 PostgreSQL보다 3배 빠름. 1/10의 비용으로 사용 데이터베이스 보안,가용성 및 안정성 제공
- 두가지 모드
  - Single-Master: 한개의 쓰기 전용 노드와 다수의 읽기 전용 노드 구성. 보통 많이 사용
  - Multi-Master: 다수의 노드로 읽기/쓰기 가능
- 용량의 자동 증감: 10GB부터 시작하여 10GB단위로 용량 증가(최대 128GB)
- 연산 능력: 96vCPU와 768GB까지 증가 가능(db.r5.24xlarge)
- 데이터의 분산 저장: 각 AZ마다 2개의 데이터 복제본 저장 x 최소 3개 이상의 AZ = 최소 6개의 복제본
  - 3개 이상을 읽어버리기 전엔 쓰기 능력 유지
  - 4개 이상 읽어버리기 전엔 읽기 능력 유지
  - 손실된 복제본은 자가 치유: 지속적으로 손실된 부분을 검사 후 복구
  - Quorum 모델 사용 (Quorum: 간단하게 투표 시스템이라고 생각하면 됨. 예를 들자면 3개 이상에 쓰여졌다면 올바른 쓰기다..)

### Single-Master 모드
- 한대의 Writer 인스턴스와 다수의 일기 전용 인스턴스(Aurora Replicas)로 구성
- 총 15개의 Replica 생성 가능
- Async 복제
- 하나의 리전 안에 생성 가능
- Writer가 죽을 경우 자동으로 Replica 중 하나가 Writer로 FailOver
  - 데이터 손실 없이 Failover 시 메인으로 승격 가능
- 고가용성(High Availability) 확보

### Multi-Master 모드
- 최대 4대의 노드가 읽기/쓰기 담당
  - 각 노드는 독립적: 정지/재부팅/삭제 등에 서로 영향 받지 않음
- 지속적인 가용성(Continuous Availiavility, High보다 높은 개념이라고..) 제공
- 주로 Multitenant 혹은 Sharding이 적용된 어플리케이션에 좋은 성능

### Aurora Global Database
- 전 세계의 모든 리전에서 1초내의 지연시간으로 데이터액세스 가능
- 재해복구 용도로 활용 가능
  - 유사 시 보조 리전 중 하나를 승격하여 메인으로 활용
  - 1초의 RPO(복구 목표 지점, Recovery Point)
  - 1분 미만의 RTO(복구 목표 시간, Recovery Time)
- 보조 리전에는 총 16개의 Read 전용 노드 생성 가능(원래는 15개)

### Aurora 병렬 쿼리
- 다수의 읽기 노드를 통해 쿼리를 병렬로 처리하는 모드
  - 빠름
  - 부하 분산(CPU, memory)
- MYSQL5.6/5.7에서만 지원
- 몇몇 낮은 인스턴스(t2,t3 등)에서는 지원하지 않음

### Aurora 백업
- 읽기 복제본(Read Replica) 지원 
  - Aurora Replica와 다른 개념.. MySQL DB의 Binary log 복제(Binlog)
  - 단 다른 리전에만 생성 가능
- RDS와 마찬가지로 자동/수동 백업 가능
  - 자동 백업의 경우 1~35일 동안 보관(S3에 보관)
  - 수동 백업(스냅샷) 가능
  - 백업 데이터를 복원할 경우 다른 데이터베이스를 생성

### Aurora DB 클로닝
- 기존의 DB에서 새로운 DB 복제
  - 스냅샵을 통해 새로운 데이터를 생성하는 것보다 빠르고 저렴함
- Copy-on-Write 프로토콜 사용
  - Clone한 DB는 기존 Original의 노드를 가리키고 있음. 둘 중 하나에 Write로 인해 변경이 생기면 그때 노드를 생성해서 분리.

### Aurora Backtrack
- 기존의 DB를 특정 시점으로 되돌리는 것(새로운 DB가 아닌 기존 DB)
  - DB 관리의 실수를 쉽게 만회 가능(예: Where 없는 Delete)
  - 새로운 DB를 생성하는 것보다 훨씬 빠름
  - 앞 뒤로 시점을 이동할 수 있기 때문에 원하는 지점을 빠르게 찾을 수 있음.
  - Aurora 생성 시 Backtrack을 설정한 DB만 가능. Multi-Master에서는 불가능.
  - 백트랙 활성화 시 시간당 DB의 변화를 저장. 저장된 용량만큼 비용 지불. DB변화가 많을수록 많은 로그=>많은 비용 발생
- Backtrack Window
  - Target Backtrack Window
    - 어느시점만큼 DB를 되돌리기 위한 데이터를 저장할 것인지 미리 설정. 지정 시점 이전으로는 백트랙 불가능
  - Actual Backtrack Window
    - 실제로 시간을 얼만큼 되돌릴지를 의미. Target Backtrack Window보다 작아야 함.







  

