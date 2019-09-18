# Katelyn 신규 설치 및 적용 전 준비 사항

## 전체 구성 확인

* Tango의 경우, Gateway와 같은 서버에서 Data를 수집하여 FlashBase에 적재
* `(Producer) - Kafka - (Katelyn) - FlashBase`의 구조로 구성되어 있음

## Binary Package 준비

* Apache Hadoop 2.6.5
  * [hadoop-2.6.5.tar.gz](https://archive.apache.org/dist/hadoop/common/hadoop-2.6.5/hadoop-2.6.5.tar.gz)
* Apache Spark 2.3.1 (FlashBase Custom Edition)
  * [spark-2.3.1-custom-v1.0-bin-nvaccel-spark.tgz](https://tde.sktelecom.com/stash/projects/FBRELEASE/repos/downloads/raw/Spark/bin/spark-2.3.1-custom-v1.0-bin-nvaccel-spark.tgz) (TDE 인증 필요)
* Apache Kafka 2.1.0
  * [kafka_2.11-2.1.1.tgz / Scala 2.11](http://mirror.navercorp.com/apache/kafka/2.1.1/kafka_2.11-2.1.1.tgz)

## 설정 파일 준비

### Hadoop / Yarn 설정 파일

* `conf/` 참조
  * [conf/hdfs-site.xml](../conf/hdfs-site.xml)
  * [conf/core-site.xml](../conf/core-site.xml)
  * [conf/yarn-site.xml](../conf/yarn-site.xml)

### Spark 설정 파일

* `conf/` 참조
  * [conf/spark-defaults.conf](../conf/spark-defaults.conf)

### ZooKeeper / Kafka 설정 파일

* `conf/` 참조
  * [conf/zookeeper.properties](../conf/zookeeper.properties)
  * [../conf/server.properties](../conf/server.properties)


### Kaetlyn 설정 파일


## 실행 스크립트 준비 (가능 시)

* Cluster 내의 Kafka 및 ZooKeeper를 일괄로 설정 / 시작할 수 있는 스크립트 작성 필요 (가능 시)
