# HAProxy 설치
> Ubuntu 20.04 버전
* ~$ sudo apt install software-properties-common -y
* ~$ sudo add-apt-repository ppa:vbernat/haproxy-1.6
* ~$ sudo apt update
* ~$ sudo apt install haproxy -y
    
    차례대로 입력한다.

> HAProxy 설치 확인
* ~$ haproxy -v
* ~$ sudo service haproxy status

> 재실행 하고싶으면 아래 코드
*  sudo
service haproxy restart