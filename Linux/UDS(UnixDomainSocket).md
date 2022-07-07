# Unix Domian Socket

    Unix domain socket(이하 UDS) 은 IPC socket (inter-process communication socket) 이라고도 하며 TCP의 소켓과 동일한 API 로 데이타를 주고 받을 수 있는 local file 기반의 소켓입니다.
    
    TCP socket 과 차이점은 local host 의 process 간의 통신이므로 속도가 매우 빠르고 메모리 소모가 적다는 장점이 있습니다.
    
    UDS 를 사용할때 또 하나의 장점은 socket 을 생성할 때 sockaddr_un 이라는 구조체를 사용하는것 외에는 일반적인 소켓을 다루는 것과 동일하므로 UDS 로 구성해 놓은 후에 성능이나 보안상의 문제로 서버를 클라이언트와 분리하기가 매우 용이하다는 점입니다.


출처 : https://www.lesstif.com/linux-core/unix-domain-socket