# Katelyn 신규 설치 및 적용 시 작업 계획서

## 시작하기 전에

* 이 문서에서는 Kaetlyn을 설치 및 실행하기 위한 **최소한**의 작업 방법을 안내합니다.
* 이 문서를 참고하여 작업을 하기 전, [prerequisite.md](./prerequisite.md)을 먼저 준비하셔야 합니다.
* 구체적인 Hadoop / Yarn / Spark / Zookeeper / Kafka 및 Spark Application에 대한 설명은 각 공식 문서를 참고해주세요.

## Hadoop / Yarn 설치 및 설정

### 1) Hadoop Binary 설치

* (Cluster 내의 모든 Hadoop Node에서 설정)
* 홈 디렉토리(`$HOME`)에 바이너리를 놓고 압축을 푼 후, 심볼릭 링크 생성

```sh
cd ~
tar -zxvf hadoop-2.6.5.tar.gz
ln -s hadoop-2.6.5 hadoop
```

### 2) Hadoop 환경 변수 설정

* (Cluster 내의 모든 Hadoop Node에서 설정)
* `$HOME/.bashrc` 또는 `$HOME/.bash_profile` 파일 내에 아래 내용 추가
* `$HADOOP_HOME` 환경변수 설정 및 `$PATH`에 추가 (& `$JAVA_HOME` 설정 확인)
* 실행 Process ID를 저장하기 위한 `$HADOOP_PID_DIR` 추가

```sh
export HADOOP_HOME=$HOME/hadoop
export HADOOP_PID_DIR=$HADOOP_HOME/pids

export PATH=${PATH}:${HADOOP_HOME}/sbin:${HADOOP_HOME}/bin

export JAVA_HOME=$(/usr/libexec/java_home)
```

* 추가 후 `source ~/.bashrc` 또는 `source ~/.bash_profile` 실행하여 환경 변수 적용

### 3) Hadoop 설정

#### 3-1) hdfs-site.xml 파일 복사

* (Cluster 내의 모든 Hadoop Node에서 설정)
* [conf/hdfs-site.xml](../conf/hdfs-site.xml) 설정 파일 복사
* 복사 위치: `$HADOOP_HOME/etc/hadoop/hdfs-site.xml`

#### 3-2) hdfs-site.xml 파일 수정

* (Cluster 내의 모든 Hadoop Node에서 설정)
* `dfs.name.dir` 확인
* `dfs.data.dir` 확인
* 각 Port 번호 확인

#### 3-3) core-site.xml 파일 복사

* (Cluster 내의 모든 Hadoop Node에서 설정)
* [conf/core-site.xml](../conf/core-site.xml) 설정 파일 복사
* 복사 위치: `$HADOOP_HOME/etc/hadoop/core-site.xml`

#### 3-4) core-site.xml 파일 수정

* (Cluster 내의 모든 Hadoop Node에서 설정)
* `fs.defaultFS` 확인
* `hadoop.tmp.dir` 확인

#### 3-5) yarn-site.xml 파일 복사

* (Cluster 내의 모든 Hadoop Node에서 설정)
* [conf/yarn-site.xml](../conf/yarn-site.xml) 설정 파일 복사
* 복사 위치: `$HADOOP_HOME/etc/hadoop/yarn-site.xml`

#### 3-6) yarn-site.xml 파일 수정

* (Cluster 내의 모든 Hadoop Node에서 설정)
* `yarn.resourcemanager.hostname` 확인
* `yarn.nodemanager.resource.memory-mb` 확인
  * Spark Executor 갯수 및 Executor 당 메모리를 고려하여 지정
  * 예. `Spark Executor 갯수` x `Executor 당 메모리` + `Spark Driver 메모리`

#### 3-7) slaves 파일 수정

* (Cluster 내의 모든 Hadoop Node에서 설정)
* 파일 위치: `$HADOOP_HOME/etc/hadoop/slaves`
* Hadoop Cluster 내의 모든 Node 주소 또는 이름을 적어줌
  * 이 때, 해당 이름은 `/etc/hosts`에 기재되어 resolving이 가능해야 함

### 4) NameNode 초기화

* (Hadoop NameNode에서만 실행)

```sh
$HADOOP_HOME/bin/hadoop namenode -format
```

## Hadoop / Yarn 실행

### 1) Hadoop 실행

* (Hadoop NameNode에서만 실행)

```sh
# 이미 HDFS가 실행 중인 경우 멈추고 시작
stop-dfs.sh
start-dfs.sh
```

### (선택) Hadoop 실행 확인

```sh
tail -c 1000 $HADOOP_HOME/logs/*-namenode-*.log # NameNode 로그 확인
tail -c 1000 $HADOOP_HOME/logs/*-datanode-*.log # DataNode 로그 확인
```

### 2) Yarn 실행

* (Hadoop NameNode에서만 실행)

```sh
# 이미 Yarn이 실행 중인 경우 멈추고 시작
stop-yarn.sh
start-yarn.sh
```

### (선택) Yarn 실행 확인

```sh
tail -c 1000 $HADOOP_HOME/logs/*-resource*.log # NameNode 로그 확인
tail -c 1000 $HADOOP_HOME/logs/*-node*.log # DataNode 로그 확인
```

## Spark 설치 및 설정

### 1) Spark Binary 설치

* (Spark Node에서만 실행)
* 홈 디렉토리(`$HOME`)에 바이너리를 놓고 압축을 푼 후, 심볼릭 링크 생성

```sh
cd ~
tar -zxvf spark-2.3.1-custom-v1.0-bin-nvaccel-spark.tgz
ln -s spark-2.3.1-custom-v1.0-bin-nvaccel-spark spark
```

### 2) Spark Version 확인

* (Spark Node에서만 실행)

```sh
cat $SPARK_HOME/RELEASE
# 아래와 같이 출력 시 정상
# Spark 2.3.1-custom-v1.0 (git revision bc42b58484) built for Hadoop 2.6.5
# Build flags: -Phadoop-2.6 -Phive -Phive-thriftserver -Pyarn
```

### 3) Spark 환경 변수 설정

* (Spark Node에서만 실행)
* `$HOME/.bashrc` 또는 `$HOME/.bash_profile` 파일 내에 아래 내용 추가
* `$SPARK_HOME` 환경변수 설정 및 `$PATH`에 추가

```sh
export SPARK_HOME=$HOME/spark
export PATH=${PATH}:${SPARK_HOME}/sbin:${SPARK_HOME}/bin
```

* 추가 후 `source ~/.bashrc` 또는 `source ~/.bash_profile` 실행하여 환경 변수 적용

### 4) Spark 설정

#### 4-1) spark-defaults.conf 복사

* (Spark Node에서만 실행)
* [conf/spark-defaults.conf](../conf/spark-defaults.conf) 설정 파일 복사
* 복사 위치: `$SPARK_HOME/conf/spark-defaults.conf`

#### 4-2) spark-defaults.conf 수정

* (Spark Node에서만 실행)
* `spark.driver.extraJavaOptions` 의 heap_dump 경로 확인

## Kafka 설치

### 1) Kafka Binary 설치

* (Cluster 내의 모든 Kafka Node에서 설정)

```sh
cd ~
tar -zxvf kafka_2.11-2.1.1.tgz
ln -s kafka_2.11-2.1.1 kafka
```

### 2) Kafka 환경 변수 설정

* (Cluster 내의 모든 Kafka Node에서 설정)
* `$HOME/.bashrc` 또는 `$HOME/.bash_profile` 파일 내에 아래 내용 추가
* `$KAFKA_HOME` 환경변수 설정 및 `$PATH`에 추가
* Kafka 실행 시 사용할 Heap Memory 옵션을 추가

```sh
export KAFKA_HOME=$HOME/kafka
export PATH=${PATH}:${KAFKA_HOME}/bin
export KAFKA_HEAP_OPTS="-Xms6g -Xmx6g -XX:MetaspaceSize=96m -XX:+UseG1GC -XX:MaxGCPauseMillis=20 -XX:InitiatingHeapOccupancyPercent=35 -XX:G1HeapRegionSize=16M  -XX:MinMetaspaceFreeRatio=50 -XX:MaxMetaspaceFreeRatio=80"
# Kafka 실행 시 Heap Memory를 6GB로 설정
```

* 추가 후 `source ~/.bashrc` 또는 `source ~/.bash_profile` 실행하여 환경 변수 적용

## ZooKeeper 설치

* Kafka에 포함된 기본 ZooKeeper를 사용하여 별도의 ZooKeeper 설치는 필요하지 않음

## ZooKeeper / Kafka Cluster 설정

### 1) ZooKeeper 설정

#### 1-1) zookeeper.properties 파일 복사

* (Cluster 내의 모든 Kafka Node에서 설정)
* [conf/zookeeper.properties](../conf/zookeeper.properties) 설정 파일 복사
* 복사 위치: `$KAFKA_HOME/config/zookeeper.properties`

#### 1-2) zookeeper.properties 파일 수정

* (Cluster 내의 모든 Kafka Node에서 **각각** 설정)
* 세부 설정 항목의 설명은 [Zookeeper 문서](https://zookeeper.apache.org/doc/r3.3.3/zookeeperAdmin.html#sc_configuration) 참조
* Zookeeper 관련 데이터가 저장될 `dataDir`을 확인 / 설정
* Client에서 접근할 Port인 `clientPort`을 확인 / 설정
* 각 Node의 접속 정보가 `server.ID`에 설정되어 있는지 확인
  * 각 서버의 접속 정보를 `IP:Port1:Port2` 형태로 정리
  * 아래 `${dataDir}/myid` 내의 각 ID 값을 갖는 서버별로 추가
  * 예. `server.1=tg-p-anv-gw01:2888:3888`
  * 예. `server.2=tg-p-anv-gw02:2888:3888`

#### 1-3) myid 파일 생성

* (Cluster 내의 모든 Kafka Node에서 **각각** *서로 다르게* 설정)
* 각 노드의 ID(숫자)를 위 `zookeeper.properties` 내 `dataDir` 설정 디렉토리 내 `myid` 파일에 작성
* 예. `zookeeper.properties` 내의 `dataDir` 항목이 `/hdd_01/zookeeper`이고, ID가 `3`인 경우: `echo 3 > /hdd_01/zookeeper/myid`

### 2) Kafka 설정

#### 2-1) server.properties 파일 복사

* (Cluster 내의 모든 Kafka Node에서 설정)
* [conf/server.properties](../conf/server.properties) 설정 파일 복사
* 복사 위치: `$KAFKA_HOME/config/server.properties`

#### 2-2) server.properties 파일 수정

* (Cluster 내의 모든 Kafka Node에서 **각각** *서로 다르게* 설정)
* 세부 설정 항목의 설명은 [Kafka 설정 문서](http://kafka.apache.org/21/documentation.html#configuration) 참조
* 각 Node ID(숫자)를 `broker.id`에 설정
  * 예. `broker.id=3`
* `advertised.listeners`에 해당 Node의 IP 주소 기재
  * 외부에서 접근해야 하므로, `/etc/hosts`에 등록된 별칭보다는 IP 주소를 입력
* `log.dirs` 설정
  * 각 Node의 Disk 숫자만큼 `log.dirs`에 기재
  * 예. 4개인 경우: `/nvdrive0/kafka/data01,/nvdrive0/kafka/data02,/nvdrive0/kafka/data03,/nvdrive0/kafka/data04`
* `log.retention.hours` 및 `log.retention.bytes` 확인
  * Kafka는 `log.segment.bytes`에 도달하는 경우 Log 파일을 새로 생성하며,
  * 과거에 생성했던 Log 파일들은 `log.retention.hours`와 `log.retention.bytes` 중 하나에 도달하면 삭제
* `zookeeper.connect` 설정
  * ZooKeeper Cluster 상의 모든 Node를 `IP:Port` 형태로 추가함
  * 이 때, 위 Zookeeper에서 설정한 `clientPort` 사용함
* Tango 환경을 위한 별도 설정이 있다면 반영
  * `replica.fetch.max.bytes=20971520`
  * `message.max.bytes=10485760`
  * `default.replication.factor=2`

## ZooKeeper / Kafka Cluster 실행

### ZooKeeper Cluster 실행

#### 1) ZooKeeper 실행

* (Cluster 내의 모든 Kafka Node에서 실행)

```sh
$KAFKA_HOME/bin/zookeeper-server-start.sh -daemon $KAFKA_HOME/config/zookeeper.properties
```

#### (선택) ZooKeeper 중단

* 필요 시 아래 명령어로 ZooKeeper 중단

```sh
$KAFKA_HOME/bin/zookeeper-server-stop.sh
```

#### (선택) ZooKeeper 실행 확인

* 아래 명령어로 ZooKeeper Shell에서 실행 확인

```sh
$KAFKA_HOME/bin/zookeeper-shell.sh IP:PORT
# 예. $KAFKA_HOME/bin/zookeeper-shell.sh tg-p-anv-gw01:2181
```

* ZooKeeper Shell 내에서 `stat /zookeeper` 명령 실행

```sh
stat /zookeeper
# cZxid = 0x0
# ctime = Thu Jan 01 09:00:00 KST 1970
# mZxid = 0x0
# mtime = Thu Jan 01 09:00:00 KST 1970
# pZxid = 0x0
# cversion = -1
# dataVersion = 0
# aclVersion = 0
# ephemeralOwner = 0x0
# dataLength = 0
# numChildren = 1
```

* ZooKeeper Shell 내에서 `quit` 명령으로 종료

```sh
quit
# Quitting...
```

### Kafka Cluster 실행 및 초기화

#### 1) Kafka 실행

* (Cluster 내의 모든 Kafka Node에서 실행)
* 각 Kafka Node에서 Kafka 실행

```sh
$KAFKA_HOME/bin/kafka-server-start.sh -daemon $KAFKA_HOME/config/server.properties
```

#### (선택) Kafka 중단

* 필요한 경우, 아래 명령어로 Kafka 중단

```sh
$KAFKA_HOME/bin/kafka-server-stop.sh
```

#### 2) Kafka 초기화 - Topic 생성

* (Cluster 내의 **하나**의 Kafka Node에서**만** 실행)
* 아래 설정 항목을 참고하여 Kafka Topic 설정
* 설정 항목
  * `--zookeeper HOSTIP:PORT` (예. `--zookeeper localhost:2181`) :
    * Topic과 Partition정보를 참고할 ZooKeeper Host IP와 ClientPort 설정
  * `--topic TOPICNAME` (예. `--topic nvkvs`)
    * 지정한 이름으로 Topic 생성
  * `--partition NUM` (예. `--partition 16`)
    * 생성할 Partition의 수
    * 적정한 Partition의 수는 `(Cluster 내 Node 수) x (Node당 Disk 갯수)` 로 설정
  * `--replication-factor NUM` (예. `--replication-factor 2`)
    * 각 Partition을 몇 개를 가질지 지정
    * 각 복제본은 서로 다른 Node에 저장
* 위 설정 항목을 참조하여 아래와 같이 Kafka Topic 생성

```sh
$KAFKA_HOME/bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 2 --partitions 36 --topic tango-prd-01
# Created topic "tango-prd-01".
$KAFKA_HOME/bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 2 --partitions 6 --topic tango-prd-err
# Created topic "tango-prd-err".
```

#### (선택) 생성한 Kafka Topic 확인

```sh
$KAFKA_HOME/bin/kafka-topics.sh --zookeeper localhost:2181 --describe --topic tango-prd-01
# Topic:tango-prd-01	PartitionCount:36	ReplicationFactor:2	Configs:
# 	Topic: tango-prd-01	Partition: 0	Leader: 4	Replicas: 4,3	Isr: 4,3
# 	Topic: tango-prd-01	Partition: 1	Leader: 5	Replicas: 5,4	Isr: 5,4
# 	Topic: tango-prd-01	Partition: 2	Leader: 6	Replicas: 6,5	Isr: 6,5
# 	Topic: tango-prd-01	Partition: 3	Leader: 1	Replicas: 1,6	Isr: 1,6
# 	Topic: tango-prd-01	Partition: 4	Leader: 2	Replicas: 2,1	Isr: 2,1
# 	Topic: tango-prd-01	Partition: 5	Leader: 3	Replicas: 3,2	Isr: 3,2
# 	Topic: tango-prd-01	Partition: 6	Leader: 4	Replicas: 4,5	Isr: 4,5
# 	Topic: tango-prd-01	Partition: 7	Leader: 5	Replicas: 5,6	Isr: 5,6
# 	Topic: tango-prd-01	Partition: 8	Leader: 6	Replicas: 6,1	Isr: 6,1
# 	Topic: tango-prd-01	Partition: 9	Leader: 1	Replicas: 1,2	Isr: 1,2
# 	Topic: tango-prd-01	Partition: 10	Leader: 2	Replicas: 2,3	Isr: 2,3
# 	Topic: tango-prd-01	Partition: 11	Leader: 3	Replicas: 3,4	Isr: 3,4
# 	Topic: tango-prd-01	Partition: 12	Leader: 4	Replicas: 4,6	Isr: 4,6
# 	Topic: tango-prd-01	Partition: 13	Leader: 5	Replicas: 5,1	Isr: 5,1
# 	Topic: tango-prd-01	Partition: 14	Leader: 6	Replicas: 6,2	Isr: 6,2
# 	Topic: tango-prd-01	Partition: 15	Leader: 1	Replicas: 1,3	Isr: 1,3
# 	Topic: tango-prd-01	Partition: 16	Leader: 2	Replicas: 2,4	Isr: 2,4
# 	Topic: tango-prd-01	Partition: 17	Leader: 3	Replicas: 3,5	Isr: 3,5
# 	Topic: tango-prd-01	Partition: 18	Leader: 4	Replicas: 4,1	Isr: 4,1
# 	Topic: tango-prd-01	Partition: 19	Leader: 5	Replicas: 5,2	Isr: 5,2
# 	Topic: tango-prd-01	Partition: 20	Leader: 6	Replicas: 6,3	Isr: 6,3
# 	Topic: tango-prd-01	Partition: 21	Leader: 1	Replicas: 1,4	Isr: 1,4
# 	Topic: tango-prd-01	Partition: 22	Leader: 2	Replicas: 2,5	Isr: 2,5
# 	Topic: tango-prd-01	Partition: 23	Leader: 3	Replicas: 3,6	Isr: 3,6
# 	Topic: tango-prd-01	Partition: 24	Leader: 4	Replicas: 4,2	Isr: 4,2
# 	Topic: tango-prd-01	Partition: 25	Leader: 5	Replicas: 5,3	Isr: 5,3
# 	Topic: tango-prd-01	Partition: 26	Leader: 6	Replicas: 6,4	Isr: 6,4
# 	Topic: tango-prd-01	Partition: 27	Leader: 1	Replicas: 1,5	Isr: 1,5
# 	Topic: tango-prd-01	Partition: 28	Leader: 2	Replicas: 2,6	Isr: 2,6
# 	Topic: tango-prd-01	Partition: 29	Leader: 3	Replicas: 3,1	Isr: 3,1
# 	Topic: tango-prd-01	Partition: 30	Leader: 4	Replicas: 4,3	Isr: 4,3
# 	Topic: tango-prd-01	Partition: 31	Leader: 5	Replicas: 5,4	Isr: 5,4
# 	Topic: tango-prd-01	Partition: 32	Leader: 6	Replicas: 6,5	Isr: 6,5
# 	Topic: tango-prd-01	Partition: 33	Leader: 1	Replicas: 1,6	Isr: 1,6
# 	Topic: tango-prd-01	Partition: 34	Leader: 2	Replicas: 2,1	Isr: 2,1
# 	Topic: tango-prd-01	Partition: 35	Leader: 3	Replicas: 3,2	Isr: 3,2

$KAFKA_HOME/kafka-topics.sh --zookeeper localhost:2181 --describe --topic tango-prd-err
# Topic:tango-prd-err	PartitionCount:6	ReplicationFactor:2	Configs:
# 	Topic: tango-prd-err	Partition: 0	Leader: 4	Replicas: 4,1	Isr: 4,1
# 	Topic: tango-prd-err	Partition: 1	Leader: 5	Replicas: 5,2	Isr: 5,2
# 	Topic: tango-prd-err	Partition: 2	Leader: 6	Replicas: 6,3	Isr: 6,3
# 	Topic: tango-prd-err	Partition: 3	Leader: 1	Replicas: 1,4	Isr: 1,4
# 	Topic: tango-prd-err	Partition: 4	Leader: 2	Replicas: 2,5	Isr: 2,5
# 	Topic: tango-prd-err	Partition: 5	Leader: 3	Replicas: 3,6	Isr: 3,6
```

#### (선택) 잘못 생성된 Topic 삭제

```sh
$KAFKA_HOME/bin/kafka-topics.sh --zookeeper localhost:2181 --delete --topic tango-prd-01
```

## Kaetlyn 설치

* `tsr2-package`에 함께 설치되어 별도 설치 필요 없음
* `tsr2-package`의 위치 확인 필요

## Kaetlyn 설정 (TBD)

* (Spark Node에서 실행)
* 다음 명령어로 Kaetlyn 설정 진행

```sh
# Kaetlyn 설정 전 FlashBase의 Cluster를 지정해야 합니다.
cfc 1
tsr2-kaetlyn edit
```

## Kaetlyn 실행 (TBD)

### 1) Kaetlyn (Consumer) 실행

* (Spark Node에서 실행)
* Kafka에 Consumer로 동작하는 Kaetlyn 실행
* Kaetlyn은 Spark Application이므로, Spark 및 Hadoop/Yarn이 실행 중인 Node에서 실행 가능
* 아래 명령어로 Kaetlyn Consumer 실행

```sh
tsr2-kaetlyn consumer start
```

### 2) Kaetlyn (Consumer) 실행 확인

* (Spark Node에서 실행)
* 아래 명령어로 Driver Log 모니터링 가능

```sh
tsr2-kaetlyn consumer monitor
```

* 아래 명령어로 Yarn Application State 확인 가능

```sh
yarn application -list
# Application State가 RUNNING으로 표기
```

### (선택) Kaetlyn Log Level 조정

* 설정 파일 편집
* 파일 위치: `$SPARK_HOME/conf/logback-kaetlyn.xml`

### (선택) Kaetlyn (Consumer) 중단

* 아래 명령어로 Kafka Consumer 중단 가능

```sh
tsr2-kaetlyn consumer stop
# SIGTERM을 받아서 실행 중인 Job을 마무리하고 Kafka offset을 업데이트하므로 실제 종료까지는 시간 필요
```

### (선택) Kaetlyn 적재 Data Offset 확인

* Consumer Offset 확인

```sh
# --group에 확인할 Group명을 써준다.
$KAFKA_HOME/bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group (Consumer 그룹명)
# 실행 예.
#    TOPIC           PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             CONSUMER-ID     HOST            CLIENT-ID
#    nvkvs           4          272904          272904          0               -               -               -
#    nvkvs           12         272904          272904          0               -               -               -
#    nvkvs           15         273113          273113          0               -               -               -
#    nvkvs           6          272906          272906          0               -               -               -
#    nvkvs           0          272907          272907          0               -               -               -
#    nvkvs           8          272905          272905          0               -               -               -
#    nvkvs           3          273111          273111          0               -               -               -
#    nvkvs           9          273111          273111          0               -               -               -
#    nvkvs           13         273111          273111          0               -               -               -
#    nvkvs           10         272912          272912          0               -               -               -
#    nvkvs           1          273111          273111          0               -               -               -
#    nvkvs           11         273112          273112          0               -               -               -
#    nvkvs           14         272904          272904          0               -               -               -
#    nvkvs           7          273110          273110          0               -               -               -
#    nvkvs           5          273111          273111          0               -               -               -
#    nvkvs           2          272905          272905          0               -               -               -
```
