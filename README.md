# smartcar bigdata project

* 목표 : 스마트카 상태정보 데이터, 스마트카 주행정보 데이터를 활용하여 스마트카 이상 징후 예측, 스마트카 운전 패턴 군집을 추출한다. 

* 개발 환경 : 윈도우 X86 (RAM:16GM, HDD:256GB) -> window10 -> virtual box 3대(Linux server01,Linux server02,Linux server03)
    *  server01 (lx01.bigdata2017.com , 192.168.1.32, CentOs 6.9 ,Java 1.7)
         * Hadoop NameNode
         * Hadoop DataNode
         * HBase Management
         * HBase Region Server
         * Cloudera Management
         * PostgresSQL
         
    *  server02 (lx02.bigdata2017.com , 192.168.1.54, CentOs 6.9 ,Java 1.7)
    
         * Hadoop DataNode 
         * Oozie     
         * Storm
         * HBase Resion     
         * Redis     
         * Hive/Spark
         * Zeppelin         
         * Zookeeper 
         * Kafka
         * Hume             
         * Flume
         
    *  server03 (lx03.bigdata2017.com , 192.168.1.55, CentOs 6.9 ,Java 1.7)
         * Hadoop DataNode
         * HBase Resion
         * Impala
         * Sqoop
         

* 아키텍쳐
![architecture](https://github.com/yonwon01/bigdata/blob/master/architecture.png)

* 데이터셋 : 스마트카 상태 데이터와 스마트카 실시간 주행 데이터 시뮬레이터 개발
   ##### 시뮬레이터 코드(https://github.com/yonwon01/smartcarlog)

* 운전자 주행 데이터는 실시간으로 1초에 최대 4kbyte씩 100대의 자동차 로그데이터 수집
* 자동차 상태 데이터는 1일에 1mbyte씩 100대의 자동차 로그데이터 수집

![domain](https://github.com/yonwon01/bigdata/blob/master/domain.png)


* 순서
   1) 로그 파일의 주기적 발생
       - > 플럼을 이용해 대용량 배치 파일 및 실시간 로그 파일을 수집
   2) 배치 로그 파일 이벤트 감지              
       - > 플럼의 Source 컴포넌트 중 SpoolDir를 이용해 주기적으로 로그 파일 발생 감지
   3) 실시간 로그 발생 이벤트 감지           
       - > 플럼의 Source 컴포넌트 중 Exec-Tail을 이용해 특정 로그파일에서 로그  생성 이벤트 감지
   4) 로그 데이터의 가비지 데이터 처리     
       - > 플럼의 Interceptor를 사용해서 정상 패턴의 데이터만 필터링
   5) 장애 발생에 대한 데이터의 안전한 보관 및 적재 처리 유지
       - > 플럼의 메모리 Channel  그리고 카프카 Broker 사용으로  수집 데이터 임시 저장
   6) 실시간 로그 파일에 대한 비동기 처리 
       - > 플럼 수집 데이터를 Kafka Sink를 이용해 Kafka Topic에 비동기 전송




























