# Fleuntd

> Fluentd 소개

    Fluentd는 로그 수집기이다. Logstash가 JVM으로 동작하는 반면 Fluentd는 C와 Ruby로 개발되어 경량화 되어있고 더 메모리를 덜 잡아먹는다. 
    
    더 경량화 시키려면 Fleuntd-bit를 사용하면 되는데 HA구성이 불가능하다는 단점이 있다.

> Fluentd가 내부에서 처리하는 데이터의 특징

    1. 이벤트 | Event

    Fluentd가 읽어들인 데이터는 tag, time, record로 구성된 이벤트(Event)로 처리된다.

    * tag : 이벤트를 어디로 보낼지 결정하기 위한 구분값
    * time : 이벤트가 발생한 시간
    * record : 데이터 (JSON)

    2. 태그 | Tag
   
    Fluentd의 특징 중에 가장 핵심은 Tag이다. 태그는 이벤트가 흘러가면서 적절한 filter, Parser 그리고 Output 플러그인으로 이동할 수 있는 기준이 된다.
    
    아래 예시의 경우 input_tail 플러그인으로 전달된 이벤트에는 dev.sample라는 태그가 붙게 된다.

```
# tag 사용 예시
<source>
  @type tail
  tag dev.sample
  path /var/log/sample.log
</source>

<match dev.sample>
  @type stdout
<match>
```
  

> Fluentd가 수집한 로그는 어디 저장되는가?

-> elasticSearch + s3
