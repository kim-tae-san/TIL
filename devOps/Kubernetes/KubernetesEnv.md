# Kubernetes Start

> 실습 환경 설치

    Virtual Box, Vagrant

> Vagrant

    Vagrant는 사용자의 요구에 맞게 시스템 자원을 할당, 배치, 배포해 두었다가 필요할 때 시스템을 사용할 수 있는 상태로 만들어 줍니다. 이를 프로비저닝이라고 한다.

    프로비저닝 코드를 원래는 개인이 원하는 곳에 구현해 사용할 수 있지만 실습할때 혼동이 없도록 베이그런트 설치 디렉터리 (c:\HashiCorp)에 프로비저닝에 필요한 모든 코드를 작성한다.

    해당 폴더에서 vagrant init 실행

    vagrant cloud에서 sysnet4admin/CentOS-k8s 이미지를 확인하고 Vagrantfile의 config.vm.box의 값을 위와같이 저장

    그리고 vagrant up 실행

    * error 
    Stderr: VBoxManage.exe: error: Failed to get device handle and/or partition ID for 0000000001811bd0 (hPartitionDevice=0000000000000a8d, Last=0xc0000002/1) (VERR_NEM_VM_CREATE_FAILED)
    
    해결방법)

    bcdedit /set hypervisorlaunchtype off
    
    이럴 경우 Docker Desktop이 정상 작동안함 에러발생

    해결방법)
    Virtual Box 6.1.30 버전이상에서는 지원함


> Vagrant 명령어

* vagrant init : 프로비저닝을 위한 기초 파일을 생성
* vagrant up : Vagrantfile을 읽어 들여 프로비저닝을 진행
* vagrant halt : 베이그런트에서 다루는 가상 머신을 종료
* vagrant destroy : 베이그런트에서 관리하는 가상 머신을 삭제
* vagrant ssh : 베이그런트에서 관리하는 가상 머신에 ssh로 접속
* vagrant provision : 베이그런트에서 관리하는 가상 머신에 변경된 설정을 적용

> Vagrantfile

    Vagrantfile을 이용하여 가상머신 정보를 입력한다. 네트워크 ip주소, 포트 포워딩, 호스트 이름 등을 정의할 수 있고, provision을 제공한다.

    추가적으로 여러대 가상머신을 띄울 수 있다.

