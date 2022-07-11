# gRPC(google Remote Procedure Call)

> gRPC란?

    gRPC의 역사는 구글의 데이터센터에서 실행되는 수많은 마이크로서비스들이 10년이상 사용한 단일 범용 RPC 인프라인 Stubby에서 시작됩니다. 구글의 내부시스템은 오래전부터 마이크로서비스 아키텍처를 받아들여 연구해왔고, 내부 서비스들을 연결하기 위해서 Stubby를 만들었습니다. 2015년 3월에 Stubby의 다음 버전을 계획하면서 소스를 오픈하기로 결정하였고, 구글의 내외부 서비스뿐만 아니라 모바일, IOT 등 다양한 엔드포인트(endpoint)에서도 활용하기 위해서 gRPC를 만들었습니다. gRPC는 높은 성능의 오픈소스 범용 RPC 프레임워크입니다.

---

## 왜 gRPC를 써야하는가? 

> 높은 생산성과 효율적인 유지보수

    gRPC는 서비스와 메시지를 정의하기 위해서 Protocol Buffers(이하 ProtoBuf)를 사용합니다. 아래와 같은 ProtoBuf의 IDL만 정의하면 높은 성능을 보장하는 서비스와 메세지에 대한 소스코드가 자동으로 생성됩니다.
```protobuf
// 헤더 부분 생략..
// Greeter 서비스 정의
service Greeter {
  // 서비스의 RPC 정의
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}
// 요청 메시지 정의
message HelloRequest {
  string name = 1;
}
// 응답 메시지 정의
message HelloReply {
  string message = 1;
}
```

    자세한 예제는 뒷 부분에서 다시 다루겠지만, 위와 같은 서비스 정의만으로 서버와 클라이언트(Stub) 코드가 자동으로 생성됩니다. 위 예제의 경우는 sayHello()라는 서비스의 비지니스 로직과 클라이언트에서 Stub을 사용하여 호출해주는 부분만 구현해주면 됩니다.


> HTTP/2 기반의 양방향 스트리밍

    gRPC는 HTTP/2를 기반으로 통신합니다. HTTP/2의 특징과 장점에 대해서는 뒷 부분에서 자세히 다루도록 하겠습니다. HTTP/2에서는 양방향 스트리밍이 가능합니다. 즉, 일반적인 요청/응답 방식이 아니고 서버와 클라이언트가 서로 동시에 데이터를 스트리밍으로 주고 받을 수 있다는 것 입니다. 이 부분도 뒤에서 예제를 통해서 자세히 다루도록 하겠습니다.

> 높은 메시지 압축률과 성능

    HTTP/2의 또다른 장점중에 하나는 HTTP를 사용하는 전송보다 높은 헤더 압축률을 보장한다는 점입니다. gRPC에서는 HTTP/2에 의한 압축뿐만 아니라 protoBuf에 의한 메시지 정의에 의해서 메시지 크기를 획기적으로 줄일 수 있습니다. 메시지의 크기가 줄어드는 것은 곧 네트워크 트래픽이 줄어드는 의미하기 때문에 시스템 리소스를 절약하고 성능을 높일 수 있습니다.

> 다양한 gRPC 생태계
 
    gRPC는 필요에 따라 활용할 수 있는 Authentication, Tracing, Load Balancing, Health Checking, API Gateway 등의 생태계를 가지고 있습니다. 벡엔드 서버를 개발 하다보면 필요한 다양한 추가 기능이나 도구를 꽤 많이 갖추고 있어서 새로운 기술 스택을 추가로 리서치해서 사용할 일이 많지 않습니다.


출처 : https://medium.com/@goinhacker/microservices-with-grpc-d504133d191d