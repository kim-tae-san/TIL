# 영속적 데이터 관리

    Data 저장을 위한 최선의 선택

    volume는 Docker(Linux에서는 /var/lib/docker/volume/)가 관리하는 Host File System의 일부에 Data가 저장됩니다.
    Non-Docker 프로세스들이 File System의 해당 부분을 수정해서는 안됩니다.
    Docker에서 Data를 존속시킬 수 있는 Best한 방법입니다.

    Host의 File System 원하는 곳에 저장
    bind mount는 Data가 Host System의 어디에든지 저장될 수 있습니다.
    저장되는 Data는 System File이거나 Directory일 수 있습니다.
    Docker Host 또는 Docker Container의 Non-Docker 프로세서들이 언제든지 저장된 Data를 수정할 수 있습니다.

    Host System의 Memory에 저장
    tmpfs mount는 Host System의 Memory에만 Data가 저장되며, 절대로 Host의 File System에는 저장되지 않습니다.

# dev-mode로 Docker 활용하기

> 컨테이너에 development workflow를 적용하기 위해서

    * 컨테이너에 소스코드를 마운트한다.
    * dev 의존성을 포함한 모든 의존성을 설치한다.
    * 파일 시스템의 변화를 관찰하기 위해 nodemon을 실행한다.

```shell
    docker run -dp 3000:3000 \
     -w /app -v "$(pwd):/app" \
     node:12-alpine \
     sh -c "yarn install && yarn run dev"
```

    * -dp 3000:3000 : background(detached) 모드로 실행하고 port매핑을 해준다
    * -w /app : working directory를 지정해주거나 현재 디렉토리에서 실행되도록 한다
    * =v "$(pwd):/app" : 호스트의 현재 디렉토리를 컨테이너의 /app 디렉토리와 bind mount 한다. 
    * node:12-alpine : base 이미지 파일이다.
    * sh -c "yarn install && yarn run dev" : 쉘 명령어 커맨드이다. 시작할때 실행된다.
  

# Multi Container Apps

    지금까지 단일 컨테이너앱을 실행해왔다. 하지만 우리는 SQLite가 아닌 MySQL을 앱에 적용해보고싶다. 그러면 어디서 MySQL을 돌리지? 같은 컨테이너에 설치해야하나? 아니면 따로 실행해야하나? 라는 의문이 든다. 보통 이럴때는 각 컨테이너는 반드시 하나의 작업을 해야하고 잘 해야한다.

    * API와 프론트엔드를 DB와 분리할 좋은 찬스이다.
    * 분리된 컨테이너는 독립적으로 버전 업데이트 하기 유리하다.
    * DB 엔진을 앱에 싣기 싫을 수 있다.
    * multiple processes는 프로세스 매니저를 필요로한다. 그럴경우 컨테이너의 복잡도를 높인다.

```shell
docker run -d \
     --network todo-app --network-alias mysql \
     -v todo-mysql-data:/var/lib/mysql \
     -e MYSQL_ROOT_PASSWORD=secret \
     -e MYSQL_DATABASE=todos \
     mysql:5.7
```

    여기서 -v todo-mysql-data라는 명령어에서 volume create를 하지 않았지만 docker에서 자동으로 생성해 준 것이다.


# MySQL과의 연결

    이제 MySQL이 잘 동작한다는걸 확인했다. 그렇지만 같은 네트워크에서 다른 컨테이너가 어떻게 동작하는지 의문이다. (각 컨테이너는 고유의 IP 주소가 있다)

    그럴때 nicolaka/netshoot 컨테이너를 사용하면 좋다. 여기에서 네트워크 이슈에 대한 디버깅이나 트러블슈팅을 위한 매우 유용한 도구들을 제공한다.

    1. nicolaka/netshoot 이미지를 사용하기 위해 같은 네트워크에 넣어준다.

    docker run -it --network todo-app nicolaka/netshoot

    2. 컨테이너 내부에서 dig 커맨드를 이용할 예정이다. 이것은 매우 유용한 DNS 툴이다. 우리는 hostname mysql을 찾아 IP address로 볼 것이다.

    dig mysql

    결과물을 보면 ANSWER SECTION에서 mysql이라는 기록과 IP address를 확인할 수 있는데, network alias에서 기록한 mysql이라는 이름으로 IP를 확인할 수 있었던 것이다. 


# Docker Compose

    Docker Compose는 멀티 컨테이너 어플리케이션을 정의하고 공유하는 것을 도와주는 뛰어난 툴이다.

    Compose와 함께라면 YAML파일을 통해 서비스와 명령을 정의하고 간단히 서비스들을 떼어낼 수 있게 된다.

    이것을 사용하면 어플리케이션의 스택을 파일에 저장할 수 있게되고 쉽게 다른사람에게 프로젝트를 배포할 수 있다. 

