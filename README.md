# smartcar bigdata project

* 목표 : 스마트카 상태정보 데이터, 스마트카 주행정보 데이터를 활용하여 스마트카 이상 징후 예측, 스마트카 운전 패턴 군집을 추출한다. 

*  스마트카 상태 데이터와 스마트카 실시간 주행 데이터 시뮬레이터 개발
##### 시뮬레이터 코드(https://github.com/yonwon01/smartcarlog)

* 개발 환경 : 윈도우 X86 (RAM:16GM, HDD:256GB) -> window10 -> virtual box 3대(server01,server02,server03)
    *  server01
         * Hadoop NameNode
         * Hadoop DataNode
         * HBase Management
         * HBase Region Server
         * Cloudera Management
         * PostgresSQL
         
    *  server02
         * Hadoop DataNode  * Oozie     * Storm
         * HBase Resion     * Redis     * Hive/Spark
         * Zeppelin         * Zookeeper * Kafka
         * Hume             * Flume
         
    *  server03
         * Hadoop DataNode
         * HBase Resion
         * Impala
         * Sqoop
         

* 순서도

![domain](https://github.com/yonwon01/bigdata/blob/master/domain.png)




























