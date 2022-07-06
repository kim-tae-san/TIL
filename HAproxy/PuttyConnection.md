# Ubuntu 20.04 WSL2로 설치

> WSL2 설치
*  WSL2란 ? Window Subsystem for Linux2 의 줄임말로 가상화를 통해 윈도우에서 리눅스를 구현할 수 있게 해주는 기능이다.
> 
터미널에서 아래 명령어 실행

dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

위 명령어들은 윈도우에서 리눅스 배포를 설치하기 위해 "Linux용 윈도우 하위 시스템 옵션 기능 사용" 및 "Virtual Machine 플랫폼 옵션 기능을 사용"하도록 설정하는 명령어이다.

> MicroSoft Store에서 Ubuntu 설치         


# Putty로 우분투에 SSH로 접속하기

> SSH란? 
* SSH는 Secure Shell의 줄임말로, 원격 호스트에 접속하기 위해 사용되는 보안 프로토콜이다. 기존 원격 접속은 텔넷을 사용했는데, 암호화를 제공하지 않기 때문에 보안상 취약하다는 단점이 있었다. 때문에 이를 암호화하는 SSH기술이 등장했던 것이다. 

> SSH의 동작원리
* https://limvo.tistory.com/21

> Putty설치 및 설정
* Putty를 설치하고 PuttyGen으로 비대칭키를 생성한다. 
* 우분투 ~/.ssh 폴더가 없으면 생성해주고 ~/.ssh/authorized_keys에 vim 에디터를 사용하여 공개키를 등록해준다.
* Putty 설정 SSH의 Auth에서 개인키를 등록해주고 ip로 접속한다