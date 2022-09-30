# Keep Alive

> Persistent Connection

    HTTP에서 persistent connection이 필요한 이유는 대표적으로 site locality가 있다. site locality란 웹에서 특정 페이지를 보여주기 위해 서버에 연속적으로 이미지 request를 보내는 것 처럼, 서버에 연속적으로 동일한 클라이언트가 여러 요청을 보낼 가능성이 높은 경우를 의미한다. 

    이러한 니즈가 계속됨에 따라 HTTP/1.1부터는 HTTP 어플리케이션이 TCP connection을 요청 마다 close하지 않고 재사용 할 수 있는 방법을 제공한다.

    이렇게 요청이 처리된 후에도 connection을 유지하는 경우를 persistent connection 이라고 표현한다. 다시말해 persistent connection이란 통신의 주체인 서버와 클라이언트 중 하나가 명시적으로 connection을 close하거나 정책적으로 close될 때 까지 계속해서 connection을 유지하는 경우를 의미한다. 

> Persistent connection이 필요한 이유 

    기본적으로 persistent connection을 사용하게 되면 TCP연결을 맺기 위해 SYN과 ACK를 주고받는 three-way handshake를 매 요청마다 맺을 필요가 없어진다. 따라서 아래와 같은 장점이 있다.

    1) 네트워크 혼잡 감소: TCP, SSL/TCP connection request수가 줄어들면서
    2) 네트워크 비용 감소: 여러 개의 connection으로 하나의 client요청을 serving하는 것보다는 한 개의 connection으로 client요청을 serving하는게 더 효율적이다. 
    3) latency감소: 3-way handshake를 맺으면서 필요한 round-trip이 줄어들기 때문에 그만큼 latency가 감소한다.

> Persistent connection과 parallel connection의 차이 

    parallel connection이란 병렬적으로 동시에 여러 connection을 맺는 것을 의미한다. parallel connection은 throughput을 늘려주긴 하지만 다음의 문제가 있다. 

    1) 매번 매 요청때마다 새로운 connection을 open하고 close해야 하기 때문에 네트워크 연결에 더 많은 시간과 bandwidth가 소모된다.
    2) TCL slow start로 인한 성능 저하
    3) parallel connection을 맺을 수 있는 수의 제한

    하지만 persistent connection을 활용하게 되면 connection establishment에 소모되는 비용을 줄이면서 위와 같은 문제를 완화할 수 있다. 물론 persistent connection은 자칫 잘못 관리하게 되면 너무 많은 idle connection이 생기게 되면서 서버에 과도한 부하가 생길 수 있기 때문에 설정을 주의해야 한다.

    또한 parallel conncetion이 단점이 있다고 해서 persistent connection이 이를 대체하는게 아니라, 둘을 적절히 혼용해서 사용할 수 있어야 한다. 실제로 많은 web 어플리케이션들은 적은 수의 parallel connection을 persistent하게 유지하고 있다.

> HTTP keep alive란?

    HTTP keep-alive는 위에서 설명한 persistent connection을 맺는 기법 중 하나로 HTTP/1.0+부터 지원하고 있다. 하나의 TCP connection을 활용해서 여러개의 HTTP request/response를 주고받을 수 있도록 해준다. keep-alive옵션은 설계상 여러 문제점이 생기면서 HTTP/1.1부터 사용되고 있지 않지만 여전히 많은 웹 어플리케이션에서 사용하고 있기 때문에 이해해 둘 필요가 있다.