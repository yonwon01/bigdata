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
1)  수집

     1) 로그 파일의 주기적 발생
          -  플럼을 이용해 대용량 배치 파일 및 실시간 로그 파일을 수집
      2) 배치 로그 파일 이벤트 감지              
          -  플럼의 Source 컴포넌트 중 SpoolDir를 이용해 주기적으로 로그 파일 발생 감지
      3) 실시간 로그 발생 이벤트 감지           
          -  플럼의 Source 컴포넌트 중 Exec-Tail을 이용해 특정 로그파일에서 로그  생성 이벤트 감지
      4) 로그 데이터의 가비지 데이터 처리     - 코드(https://github.com/yonwon01/smartcar.Interceptor)
          -  플럼의 Interceptor를 사용해서 정상 패턴의 데이터만 필터링 
      5) 장애 발생에 대한 데이터의 안전한 보관 및 적재 처리 유지
          -  플럼의 메모리 Channel  그리고 카프카 Broker 사용으로  수집 데이터 임시 저장
      6) 실시간 로그 파일에 대한 비동기 처리 
          -  플럼 수집 데이터를 Kafka Sink를 이용해 Kafka Topic에 비동기 전송
          
![logsimulator](https://github.com/yonwon01/bigdata/blob/master/logsimualtor.png)


2) 적재

   1) 100대의 자동차의 상태 정보를 일 단위로 취합       
       - 수집 발생 시점을 HdfsSink에 전달해서 해당 날짜 단위로 적재

   2) 1일 220만 건의 약 100MB의 상태정보에 대한 적재 
       - 1년에 약 8억 건이 적재되며 연단위 분석에 하둡의 분산 병렬 처리 사용 

   3) 상태 정보의 발생일과 수집/적재 되는 날이 다르다  
       - 적재되는 모든 데이터에 수집/적재 처리일을 추가

   4) 적재된 정보를 일/월/년 단위로 분석해야 한다        
       - HDFS에 수집 일자별로 디렉토리 경로를 만들어서 적재

   5) 적재가 완료되면 원천 파일은 삭제한다.                 
       - 플럼의 Source 컴포넌트 중 SpoolDir의 DeletePolicy 활용
       
       
  -- 자동차 상태 데이터 적재


   -- 실시간 주행 데이터 적재
1)storm의 spout이 kafka의 topic으로 부터 차량의 실시간 주행 데이터를 받아
hbaseblot로 보내고 esper epl에서 정의한 조건에 따라 과속한 차량의 정보를
redissplot으로 보냄
2)hbase의 table에  ‘차량번호 + 발생일시’ 를 Row키로 8개의 컬럼(발생일시, 차량번호, 가속페달, 브레이크 페달, 운전대 회전각, 방향지시등, 주행속도, 주행구역)의 구조로 모든 차량의 주행정보가 적재
3) 레디스에는 현재 날짜를 키로 해서 과속한 차량의 정보를 세트 데이터 구조로 적재한다. 적재 영속 시간은 5시간으로 지나면 메모리에서 삭제
--storm topology및 bolt구현



























