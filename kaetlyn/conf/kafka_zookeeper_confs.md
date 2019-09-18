#### ZooKeeper 설정 파일 및 설명

* 파일명
  * $KAFKA_HOME/conf/zookeeper.properties
  * 별도로 ZooKeeper를 설치하지 않고, Kafka 내에 포함된 ZooKeeper를 사용
* 주요 설정 항목
  * `dataDir`
    * 각종 정보를 저장하는 위치
    * 예. `dataDir=/hdd_01/zookeeper`
  * `clientPort`
    * ZooKeeper Client가 ZooKeeper Server에 접속할 때 사용할 Port
    * 예. `clientPort=2181`
  * `maxClientCnxns`
    * (TBD)
    * 예. `maxClientCnxns=0`
  * `initLimit`
    * (TBD)
    * 예. `initLimit=5`
  * `syncLimit`
    * (TBD)
    * 예. `syncLimit=2`
  * `server.ID`
    * 각 서버의 접속 정보, `IP:Port1:Port2` 형태
    * 아래 `${dataDir}/myid` 내의 각 ID 값을 갖는 서버별로 추가
    * `Port1`은 ZooKeeper Server 간의 통신을 위해 사용
    * `Port2`는 Leader Node 선출을 위해 사용
    * 예. `server.1=apollo-w07:2888:3888`

* 파일명
  * 위 `dataDir` 내에 ID(숫자)가 포함된 `myid` 파일 작성
    * 파일명: `${dataDir}/myid`
    * 내용: ID(숫자)
    * 예. `echo 1 > /tmp/zookeeper/myid`

#### Kafka 설정 파일 및 설명

* 파일명
  * $KAFKA_HOME/conf/server.properties
* 주요 설정 항목
  * `broker.id`
    * Broker ID
    * 각 Node마다 식별 가능하도록 서로 다른 ID(숫자)를 기입
    * 예. `broker.id=1`
  * `zookeeper.connect`
    * ZooKeeper 주소
    * ZooKeeper Cluster 내의 각각 `IP:Port`를 `,`으로 연결하여 지정
    * 예. `zookeeper.connect=apollo-w07:2181,apollo-w08:2181`
  * `log.dirs`
    * Data 저장 위치
    * Load 분산을 위해 Disk마다 디렉토리를 하나씩 할당하는 것을 권장
    * 예. `log.dirs=/hdd_01/kafka,/hdd_02/kafka,/hdd_03/kafka,/hdd_04/kafka`
  * `log.retention.hours`
    * Data 보관 기한
    * 기본 값은 168시간 (= 7일)
    * 예. `log.retention.hours=168`
  * `log.retention.bytes`
    * Partition의 물리적 최대 크기
    * 크기 초과 시 디스크에서 삭제
    * 기본 값은 -1 (무제한)
    * 예. `log.retention.bytes=-1`
