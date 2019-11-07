---
  layout: post
  date: 2019-11-05
  title : Logback 로깅을 Json 형태로 저장하기
  categories: Spring
---

ELK, EFK든 중앙 로깅 시스템 진행시 엘라스틱 서치 데이터 포맷에 맞추기 위해서는 json 형태로  
로깅이 전송 되어야 한다.  

각 application server에서 filebeat등을 이용하여 json을 컨버팅 하는 방법도 있겠지만,  
필자의 경우 queue서버(aws kinesis)로 각 application이 바로 전송하는 방식을 선택 하였기  
때문에 로그백 자체에서 json으로 로그를 저장하는 방식을 찾아 보았다.


### Logback JsonLayout을 이용  (https://www.baeldung.com/java-log-json-output)
  * pom.xml에 아래 dependency들을 추가
    ```
        <dependencies>
            <dependency>
                <groupId>ch.qos.logback</groupId>
                <artifactId>logback-classic</artifactId>
                <version>1.1.7</version>
            </dependency>

            <dependency>
                <groupId>ch.qos.logback.contrib</groupId>
                <artifactId>logback-json-classic</artifactId>
                <version>0.1.5</version>
            </dependency>

            <dependency>
                <groupId>ch.qos.logback.contrib</groupId>
                <artifactId>logback-jackson</artifactId>
                <version>0.1.5</version>
            </dependency>

            <dependency>
                <groupId>com.fasterxml.jackson.core</groupId>
                <artifactId>jackson-databind</artifactId>
                <version>2.9.3</version>
            </dependency>
        </dependencies>
    ```

  * 로그백 설정 파일(보통은 logback-spring.xml)에 appneder 설정
  ```
    <appender name="json" class="ch.qos.logback.core.ConsoleAppender">
        <layout class="ch.qos.logback.contrib.json.classic.JsonLayout">
            <jsonFormatter
                class="ch.qos.logback.contrib.jackson.JacksonJsonFormatter">
                <prettyPrint>true</prettyPrint>
            </jsonFormatter>
            <timestampFormat>yyyy-MM-dd' 'HH:mm:ss.SSS</timestampFormat>
        </layout>
    </appender>

    <logger name="jsonLogger" level="TRACE">
        <appender-ref ref="json" />
    </logger>
  ```
  * prettyPrint 설정을 true로 하면 콘솔에 이쁘게(줄바꿈 되어서) 나오긴 하지만 엘라스틱에  
    개행문자('/n') 가 포함되기 때문에 전송하기 위해서는 false로 설정 하는 편이 좋다.

  * 위와 같이 설정하면 기본적으로 아래와 같은 output이 나온다.
  ```
    {
      "timestamp" : "2017-12-14 23:36:22.305",
      "level" : "DEBUG",
      "thread" : "main",
      "logger" : "jsonLogger",
      "message" : "Debug log message",
      "context" : "default"
    }
  ```
  * json 객체에 필드를 커스텀 하여 추가 하고 싶다면 MDC를 이용해야 한다.  
  ```
     MDC.put("customKey", "aaaa");

     #결과
     {
       "timestamp" : "2017-12-14 23:36:22.305",
       "level" : "DEBUG",
       "thread" : "main",
       "logger" : "jsonLogger",
       "message" : "Debug log message",
       "context" : "default",
       "mdc" : {
          "customKey" : "aaaa"
        }
     }
  ```  
  
---

###  Logstash Logback Encoder 이용 (https://www.baeldung.com/java-application-logs-to-elastic-stack)
  개별 로직에서(service나 controller) 에서 로깅한 내용을 검색하려면 message 필드를 정규식으로 검색  
  해야 하는데, 검색에 용이하고자 json 객체에 따로 필드로 추가 하고 싶은 경우가 있을 수 있다.  

  MDC를 이용하면 가능 하긴 하지만 2뎁스이상 되어야 하고 1depth에 필드를 추가 하고 싶다면,  
  Logstash Logback Encoder로 바꾸는게 답인것 같다.  

  공식 github 페이지를 보면 잘 설명 되어 있다.  

  * pom.xml dependency 추가  
 ```
  <dependency>
      <groupId>net.logstash.logback</groupId>
      <artifactId>logstash-logback-encoder</artifactId>
      <version>4.11</version>
  </dependency>
 ```
  * logback 설정 .xml 파일에서 아래와 같이 설정  

  ```
      <appender name="STASH" class="ch.qos.logback.core.ConsoleAppender">
          <file>logback/redditApp.log</file>
          <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
              <fileNamePattern>logback/redditApp.%d{yyyy-MM-dd}.log</fileNamePattern>
              <maxHistory>7</maxHistory>
          </rollingPolicy>  
          <layout class="net.logstash.logback.layout.LogstashLayout"> <!-- json 포맷 -->
              <timestampPattern>yyyy-MM-dd' 'HH:mm:ss.SSS</timestampPattern>
              <customFields><!-- 추가 커스텀 필드 -->
                  {
                      "appname" : "${app.name}",
                      "port":"${server.port:-8080}"
                  }
              </customFields>
          </layout>
      </appender>
      <root level="DEBUG">
          <appender-ref ref="STASH" />        
      </root>
  ```
  * <customFileds>에 추가로 설정하고자 하는 필드를 설정 하면 application 전체에 적용 되고,  
    개별 로직에서 특별히 필드를 추가하고자 한다면 아래와 같이 하면 된다.  

  ```
    //logstash markers append 임포트
    import static net.logstash.logback.marker.Markers.append;

    //로깅시 ppend 메소드 사용
    log.info(append("myCustomField", "hello"), "add key to log json");

  ```  
  output 결과
  ```
       {
         "@timestamp":"2019-11-04 19:13:01.018",
         "@version":"1",
         "message":"add key to log json",
         "logger_name":"com.sample.logappender.SampleController",
         "thread_name":"http-nio-8080-exec-3",
         "level":"INFO",
         "level_value":20000,
         "HOSTNAME":"AD01277912",
         "myCustomField":"hello",
         "appname":"appender-sample",
         "port":"8080"
       }

  ```

### 결론
  json 필드를 커스텀 하기에는 logstash-encoder를 활용하는 것이 편리한 듯 하다.  
  테스트로 logstash-encoder 에서 MDC.put을 이용해 보았는데 mdc 객체 필드가 생기지  
  않고 바로 json 객체에 해당 key가 생성 되었다. 
