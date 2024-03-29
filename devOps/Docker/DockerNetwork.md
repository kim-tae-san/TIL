# Docker Network

    Docker의 컨테이너는 서버의 물리 NIC와 별도로 각 컨테이너 마다 가상 NIC가 할당되어 있다. 또한 이러한 가상 NIC는 docker0이라는 가상 bridge에 접속하여 컨테이너끼리 통신한다. docker0은 Docker 데몬을 기동한 후에 생성되며 172.17.42.1 주소가 할당된다. 
     
    Docker에서 컨테이너를 구동하면 컨테이너에 172.17.0.0/16 subnet mask를 가진 private IP Address가 eth0에 자동으로 할당된다. eth0에는 호스트OS에 생성된 가상 NIC(vethxxx)가 페어로 할당된다. veth는 OSI 7 계층 Layer2의 가상 네트워크 인터페이스로서 페어된 NIC끼리 터널링 통신을 수행한다.

> Docker 컨테이너 간의 통신

    동일한 호스트상의 Docker 컨테이너는 구동 시 private Address가 자동으로 할당되므로 컨테이너끼리 통신하기 위하여 '링크 기능'을 사용한다. 이를 사용하면 한 호스트 상에 여러 컨테이너가 동작하는 경우, 컨테이너의 alias명을 통해 서로 다른 컨테이너에 접속할 수 있다. 컨테이너를 구동할 때, 통신하려는 컨테이너 이름을 지정하여 링크하면 해당 정보가 /etc/host에 환경변수로 저장되어 직접 통신할 수 있다.

    Docker는 일반적으로 1 컨테이너 1 프로세스로 운영된다. 웹 서버용 컨테이너와 DB서버용 컨테이너에서 데이터를 연계하거나 각종 서버용 컨테이너의 로그 파일을 로그용 컨테이너에 출력하는 방식으로 사용할 수 있다.

    단, 링크 기능을 사용한 통신은 동일 호스트, 즉 가상 bridge docker0에 접속한 컨테이너끼리만 가능하다. 멀티 호스트 환경에서는 링크 기능을 이용한 통신이 불가능하므로 주의해야 한다. 또한 보안 요구 사항에 따라 컨테이너 간 통신을 차단하도록 설정할 수 있다.

> Docker 컨테이너와 외부 네트워크 통신

    Docker 컨테이너가 외부 네트워크와 통신할 때에는 가상 bridge docker0와 호스트OS의 물리 NIC에서 패킷을 전송해야 한다. 이 때, Docker에서는 NAPT기능을 사용하여 접속한다. 

    NAPT(Network Address Port Translation)란 하나의 IP Address를 여러 컴퓨터에서 공유하는 기술로서 IP Address와 포트 번호를 변환하는 기능이다. private IP Address와 global IP Address를 상호 변환하는 기술로, TCP 및 UDP 포트 번호까지 동적으로 변환하기 때문에 여러머신에서 한 global IP Address로 접속할 수 있다. Docker에서는 Linux의 iptables를 NAPT에 사용하고 있다.

    이 기능은 컨테이너를 구동할 때, 내부에서 사용하고 있는 포트를 가상 bridge docker0에 개방할 때 사용된다. 예를 들어 컨테이너 구동 시, 컨테이너 내의 웹 서버(http 데몬)가 사용하는 80번 포트를 호스트OS의 8080번 포트로 전송하도록 설정하였을 때, 외부 네트워크에서 호스트의 8080번 포트에 액세스하면 컨테이너 내의 80번 포트로 연결되는 구조이다.