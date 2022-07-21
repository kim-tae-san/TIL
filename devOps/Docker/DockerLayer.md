# Docker layer

    Docker 이미지를 Pull 받을때 Layer를 한 줄 한 줄을 나누어서 받는다. Docker이미지는 이러한 layer들이 쌓인 모습인 것이다. 각 레이어들은 기본적으로 읽기전용으로 수정이 불가능하다. 레이어의 최상단에 컨테이너 전용으로 쓰기 가능한 레이어를 한 층 더 쌓아서 컨테이너에서 일어나는 모든 변경 사항을 이 레이어에 저장하는 것이다. 


# 도커 기본 데이터 저장 경로


    Docker는 overlay2 드라이버를 사용한다.
    도커는 유니온 마운트(계층화된 파일 시스템)를 지원하는 다양한 스토리지 드라이버를 지원하고 있습니다. 현재 리눅스 커널에는 Overlayfs가 포함되어있어서 이를 사용하는 경우가 많습니다.

    도커의 데이터는 기본적으로 시스템 상의 /var/lib/docker/에 저장되며 overlay2 드라이버로 저장된 레이어 데이터는 다시 image/overlay2/layerdb/sha256 아래에 저장됩니다.
```command
/var/lib/docker/image/overlay2/layerdb/sha256

$ ls -1

77fcff986d3b13762e4777046b9210a109fda20cb261bd3bbe5d7161d4e73c8e/

831c5620387fb9efec59fc82a42b948546c6be601e3ab34a87108ecf852aa15f/

dc8adf8fa0fc82a56c32efac9d0da5f84153888317c88ab55123d9e71777bc62/
```

    해당 경로로 이동

    3개의 다이제스트값으로 된 디렉터리가 있는 것을 확인한다. 도커 이미지를 받을 때 출력된 아이디와 저장된 레이어의 아이디와 다르다. 이미지를 가져올 때 출력되는 다이제스트 값은 도커 레지스트리에서 관리하는 고유 아이디이다. 레이어의 아이디는 별개로 있다. docker image inspect 명령으로 레이어 목록을 확인할 수 있다. RootFS의 Layers에 보면 sha256 디렉토리 아래의 디렉토리 이름과 같다는 것을 알 수 있다.

```
$ docker image inspect nginx | jq  '.[].RootFS'
{
  "Type": "layers",
  "Layers": [
    "sha256:831c5620387fb9efec59fc82a42b948546c6be601e3ab34a87108ecf852aa15f",
    "sha256:5fb987d2e54d85820d95d6c31f3fe4cd95bf71fe6d9d9e4684082cb551b728b0",
    "sha256:4fc1aa8003a3d0d2481f10d17773869cbff12c1008df30e0bab8259086a0311c"
  ]
}
```

    sha256 디렉토리 내부의 각 레이어의 id로 저장되어있는 디렉토리로 들어가보면 

```command
$ cd 831c5620387fb9efec59fc82a42b948546c6be601e3ab34a87108ecf852aa15f
$ ls -1
cache-id
diff
size
tar-split.json.gz
```

    여기에 레이어에 포함된 파일이 있는 것 같지는 않네요. 실제 데이터는 또 다른 곳에 있습니다. 파일 목록 중에 cache-id라는 파일이 있습니다. 이 값을 출력해보면 실제 데이터가 있는 디렉터리의 다이제스트 값이 출력됩니다.

```
$ cat cache-id ; echo
e5b51f307392f7a3776edaa67d5d14b85e04dad9aeca753ac6ad30aaeaa55152
```

    레이어 ID는 고유한 값이지만, cache-id는 고유한 값이 아니므로 이미지를 풀 받은 시스템 마다 달라집니다. 이 디렉터리는 /var/lib/docker 바로 아래의 overlay2 아래에 있습니다. 정확히는 다이제스트 이름을 가진 디렉터리 아래의 diff 파일에 레이어의 컨텐츠가 들어있습니다.

```
$ pwd
/var/lib/docker/overlay2/
$ ls
10a07b3d72ac36291843eb6ca01698649220065d3b3046f63546fcee49c3c36f
7e5bc8d3a02343bf40d479979e734343faff52b8fc768959a24e860c30ae4b74
e5b51f307392f7a3776edaa67d5d14b85e04dad9aeca753ac6ad30aaeaa55152
l
$ cd e5b51f307392f7a3776edaa67d5d14b85e04dad9aeca753ac6ad30aaeaa55152/diff
$ ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

# Docker Layer 계층 이해하기

    이미지와 레이어는 도커 1.10전에는 크게 구별이 없었다. 따라서 
    
    docker images -a

    명령어를 통해 현재 도 데몬에서 사용가능한 이미지 목록을 출력해 보면 

```
# docker images -a
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
nginx               latest              bee5d581ef8b        3 weeks ago         126MB
<none>              <none>              e9a8bb928312        3 weeks ago         126MB
<none>              <none>              eb22865337de        3 weeks ago         126MB
<none>              <none>              a8222839283b        3 weeks ago         126MB
<none>              <none>              abffffe8923f        3 weeks ago         126MB
...
<none>              <none>              000eee12ec04        2 months ago        69.2MB
<none>              <none>              382b287acfab        2 months ago        69.2MB
```

    이런 느낌으로 나왔었다. 

    요즘에는 

```
$ docker images -a
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              231d40e811cd        3 weeks ago         126MB
```

    이렇게 레이어를 모두 보여주지는 않고 nginx 이미지 하나만 출력된다.

    여기에는 크게 두 가지 차이점이 있습니다. 하나는 레이어가 이미지로 출력된다는 점과 레이어의 갯수가 풀 받을 때보다 많다는 점입니다. 먼저 레이어가 이미지 ID를 가지고 있기 때문에 이를 사용해 컨테이너를 실행하는 것이 가능했습니다. 하지만 지금은 도커 이미지를 풀 받을 때 레이어는 레이어로 남겨두고 최종 레이어를 기반으로 한 이미지만을 이미지로 사용할 수 있습니다. 따라서 현재는 풀 받은 이미지의 중간 레이어로 컨테이너를 실행시키는 것이 (불가능하지는 않지만) 쉽지 않습니다.

    이미지 갯수는 왜 다른 걸까요? 이는 docker hisotry 명령어로 이유를 찾아볼 수 있습니다. docker history는 이미지가 만들어지기 까지의 과정 전체를 보여주는 명령어입니다.

```
$ docker history nginx:latest
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
231d40e811cd        3 weeks ago         /bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon.   0B
<missing>           3 weeks ago         /bin/sh -c #(nop)  STOPSIGNAL SIGTERM           0B
<missing>           3 weeks ago         /bin/sh -c #(nop)  EXPOSE 80                    0B
<missing>           3 weeks ago         /bin/sh -c ln -sf /dev/stdout /var/log/nginx.   22B
<missing>           3 weeks ago         /bin/sh -c set -x     && addgroup --system -.   57.1MB
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV PKG_RELEASE=1~buster     0B
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV NJS_VERSION=0.3.7        0B
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV NGINX_VERSION=1.17.6     0B
<missing>           3 weeks ago         /bin/sh -c #(nop)  LABEL maintainer=NGINX Do.   0B
<missing>           3 weeks ago         /bin/sh -c #(nop)  CMD ["bash"]                 0B
<missing>           3 weeks ago         /bin/sh -c #(nop) ADD file:bc8179c87c8dbb3d9.   69.2MB
```

    Dockerfile을 작성해본 적이 있다면 결과값에 보이는 내용이 도커 파일로 이미지를 빌드한 과정이라는 것을 어렴풋이 알아보실 겁니다. 이 이미지는 베이스 이미지를 포함해 총 11단계의 명령어로 작성되었습니다. 예전에는 11단계 모든 단계가 레이어로 다뤄졌습니다만, 현재는 이미지에 메타데이터로 저장되는 부분은 레이어로 다뤄지지 않습니다. 따라서 현재는 CMD, LABEL, ENV, EXPOSE, STOPSINAL과 같은 메타데이터를 다루는 부분은 레이어로 저장되지 않고, ADD나 RUN이 일어나는 3단계만이 레이어로 저장됩니다. 이는 풀 받을 때 출력되는 레이어 갯수와 일치합니다. (달리 말하면 예전에는 같은 이미지에 대해서 더 많은 레이어를 받아왔을 것입니다.)

    histroy의 출력 결과에서도 알 수 있습니다만 중간 레이어들의 이미지 ID 값은 <missing> 상태입니다. 따라서 중간 레이어로 컨테이너를 실행할 수 없습니다. 그렇다고 아예 방법이 없는 건 아닌데, 중간 레이어에서 컨테이너를 실행하기 위해서는 현재 머신에 직접 도커 빌드를 수행해야합니다. 

# 컨테이너의 레이어 계층 이해

    도커 이미지가 어떻게 만들어지는지 이해하려면 먼저 컨테이너의 레이어 구조에 대해서 이야기해야합니다. 컨테이너를 사용해보셨다면 귀에 못이 박히도록 도커는 계층화된 파일 시스템을 이용하고, 이미지는 불변이라는 얘기를 들어오셨을 겁니다. 그럼 여기서는 실제로 컨테이너를 실행핬을 때 레이어 계층이 어떻게 구성되는지 살펴보겠습니다.

    위 이미지 레이어 중에서 실제로 레이어로 사용되는 3줄만 남기고 이름을 붙여보겠습니다.

```
nginx-layer3           3 weeks ago         /bin/sh -c ln -sf /dev/stdout /var/log/nginx.   22B
nginx-layer2           3 weeks ago         /bin/sh -c set -x     && addgroup --system -.   57.1MB
nginx-layer1           3 weeks ago         /bin/sh -c #(nop) ADD file:bc8179c87c8dbb3d9.   69.2MB
```

    레이어는 아래에서부터 위로 쌓아올라갑니다. 따라서 layer1이 바닥에 있고, 그 위에 layer2가 올라가고 마지막으로 layer3가 위에 올라가서 비로소 이미지가 됩니다. 그리고 다시 한 번 이야기하지만, 이미지의 레이어들은 모든 읽기 전용입니다. 컨테이너에서 무슨 짓을 하더라도 절대로 이미지의 내용이 달라지지 않습니다.

```
nginx-layer3(RO) = nginx:latest
nginx-layer2(RO)
nginx-layer1(RO)
```

    유니온 마운트를 이해하지 못 하더라도 일단 레이어들을 쌓아서 이미지가 만들어진다는 느낌은 오셨을 겁니다. 여기서 등장하는 게 컨테이너입니다. 그럼 대체 컨테이너를 실행하면 무슨 일이 벌어지는 걸까요? 답은 간단합니다. 컨테이너를 실행할 때 아주 깨끗한 레이어를 하나 이미지의 최상위 레이어 위에 올려줍니다. 컨테이너를 한 대 실행시켜보겠습니다.

```
$ docker run -it nginx:latest bash
root@34a6aa18a83c:/#
```

    방금 생성한 컨테이너 아이디(호스트네임)는 34a6aa18a83입니다. 임의로 이 이름을 붙여 컨테이너와 함께 만들어진 레이어를 container-layer-34a6aa18a83c라고 이름붙여보겠습니다. 따라서 
    컨테이너 실행시에 마운트되는 구조는 다음과 같습니다.

```
container-layer-34a6aa18a83c(RW)
nginx-layer3(RO) = nginx:latest
nginx-layer2(RO)
nginx-layer1(RO)
```

    container-layer-34a6aa18a83c 레이어에는 아무것도 없습니다. 하지만 컨테이너에서 파일 목록을 확인해보면 그 아래 레이어에 있는 내용들이 그대로 보이는 것을 알 수 있습니다. 좀 더 정확히 이야기하자면 nginx-layer1, nginx-layer2, nginx-layer-3, container-layer-34a6aa18a83c의 파일 전체를 마운트한 작업 디렉터리가 하나 있고, 이후에 이 디렉터리에서 일어나는 모든 작업(파일 변경)은 최상위 레이어인 container-layer-34a6aa18a83c에 저장됩니다.

    다시 한 번 강조하지만, 따라서 그 아래에 있는 레이어들에는 어떠한 변경도 일어나지 않습니다. 아래 레이어에 있었던 파일을 삭제하면 어떻게 될까요? 그걸 처리해주는 게 바로 유니온 마운트의 역할입니다. 이 또한 최상위 레이어에 기록되고, 그 아래에 있는 레이어들에는 어떠한 영향도 주지 않습니다. 따라서 파일을 좀 삭제하고 새로운 컨테이너를 실행하더라도 기존 이미지의 내용이 그대로 남이있습니다.

    그럼 도커 데몬을 통해서 실제 컨테이너의 마운트 구조를 확인해보도록하겠습니다.

```
$ docker inspect 34a6aa18a83c | jq '.[].GraphDriver'
{
  "Data": {
    "LowerDir": "/var/lib/docker/overlay2/1f801c214d32d4ccd6e34e4185cca9707fd9b8ec28e2b63b857546e2b53568a1-init/diff:/var/lib/docker/overlay2/10a07b3d72ac36291843eb6ca01698649220065d3b3046f63546fcee49c3c36f/diff:/var/lib/docker/overlay2/7e5bc8d3a02343bf40d479979e734343faff52b8fc768959a24e860c30ae4b74/diff:/var/lib/docker/overlay2/e5b51f307392f7a3776edaa67d5d14b85e04dad9aeca753ac6ad30aaeaa55152/diff",
    "MergedDir": "/var/lib/docker/overlay2/1f801c214d32d4ccd6e34e4185cca9707fd9b8ec28e2b63b857546e2b53568a1/merged",
    "UpperDir": "/var/lib/docker/overlay2/1f801c214d32d4ccd6e34e4185cca9707fd9b8ec28e2b63b857546e2b53568a1/diff",
    "WorkDir": "/var/lib/docker/overlay2/1f801c214d32d4ccd6e34e4185cca9707fd9b8ec28e2b63b857546e2b53568a1/work"
  },
  "Name": "overlay2"
}
```

    여기에 컨테이너를 실행할 때 레이어가 마운트되는 모든 정보가 나와있습니다. 이 내용을 이해하기 위해서는 OverlayFS에 대한 이해가 필요합니다. 우선은 LowerDir에 이미지 레이어들이 포함되고, UpperDir가 컨테이너 레이어가 된다는 정도만 이해하면 됩니다(OverlayFS에 대해서는 뒤에서 조금 더 다룹니다).

    컨테이너를 여러개 실행하더라도 이미지 레이어만 공유할 뿐 각각의 컨테이너 전용 쓰기 레이어가 만들어지기 때문에 서로 영향을 주지 않습니다. 컨테이너를 2개 실행하면 다음과 같은 구조가 됩니다.

```
container:34a6aa18a83c
--------------------------------
container-layer-34a6aa18a83c(RW)
nginx-layer3(RO) = nginx:latest
nginx-layer2(RO)
nginx-layer1(RO)


container:47a8bff23fba
--------------------------------
container-layer-47a8bff23fba(RW)
nginx-layer3(RO) = nginx:latest
nginx-layer2(RO)
nginx-layer1(RO)
```

    따라서 컨테이너를 아무리 많이 실행시키더라도 실제 쓰기가 이루어지는 레이어가 분리되어있기 때문에 컨테이너들은 서로 영향을 주지 않을 것입니다. 즉, 모든 컨테이너는 고유한 쓰기 영역을 가지며, 이는 최상위 레이어가 되고 하위 레이어에는 어떠한 영향도 주지 않습니다.

    여기까지 이해했으면 본격적으로 이미지가 어떻게 만들어지는지 살펴보겠습니다.


# docker diff와 docker commit으로 이미지 만들기

    도커 이미지를 직접 만든다고 하면 Dockerfile을 떠올리실 겁니다. Dockerfile에는 DSL로 정의된 명령어들로 한 단계씩 도커 이미지 생성 과정을 기술하고, 이를 도커로 실행하면 짠하고 도커 이미지가 만들어집니다. 그런데 도커는 Dockerfile로 어떻게 이미지를 생성하는 걸까요?

    이를 이해하기 위해서 docker diff와 docker commit 명령어를 사용해보겠습니다. 실제 업무에서는 거의 사용할 일이 없는 명령어들이니 처음 보시더라도 놀라지 마시기 바랍니다. docker diff 명령어부터 알아보겠습니다. 앞서 컨테이너를 실행했을 때 레이어 구성이 어떻게 되는지 살펴보았습니다.

```
container-layer-34a6aa18a83c(RW)
nginx-layer3(RO)
nginx-layer2(RO)
nginx-layer1(RO)
```

    컨테이너에서 보이는 파일들은 이 계층들이 모두 합쳐진 모습입니다. 컨테이터는 기본적으로 이미지 계층의 파일들을 기반으로 실행되고, 최상단의 컨테이너 전용 레이어는 처음에는 텅 빈 상태입니다. 그리고 컨테이너에서 일어나는 모든 변경사항들은 최상단 레이어에 기록됩니다. 도커를 처음 배울 때 컨테이너를 종료시키면 변경된 파일들이 날아간다는 얘기를 많이 들어보셨을 겁니다. 그 이유가 여기에 있습니다. 컨테이너에서 일어나는 모든 변경사항은 container-layer-34a6aa18a83c 이 레이어에 저장되고, 컨테이너가 삭제되면 같이 날려버립니다. 즉, 컨테이너 레이어는 기본적으로 휘발적으로 사용됩니다.

    docker diff는 컨테이너 레이어의 변경사항을 보여주는 명령어입니다. 먼저 컨테이너를 처음 실행했을 때 정말로 아무런 변경사항이 없는지 확인해보겠습니다. 먼저 nginx:latest 이미지를 기반으로 bash 셸 컨테이너를 실행해보겠습니다.


```
$ docker run -it nginx bash
root@238b6c789417:/#
```

    다른 창을 하나 더 띄워서 이 컨테이너에 docker diff를 실행해봅니다.

    $ docker diff 238b6c789417

    아무것도 출력되지 않으면 정상입니다. 왜냐면 아직 컨테이너 레이어에 아무런 변경사항이 없기 때문입니다. 이 디렉터리를 실제로 확인해보겠습니다. docker inspect에서 현재 마운트된 파일 시스템의 UpperDir을 확인합니다.

```
$ docker inspect 238b6c789417 | jq '.[].GraphDriver.Data.UpperDir'
/var/lib/docker/overlay2/2d7509079a2bfa110cdfcc26df3232690fa7b970298926a08f47fa22083b6815/diff
```

    이 컨테이너에서 일어나는 모든 변경사항은 이 디렉터리에 저장됩니다. 이 디렉터리에 가서 비어있는 것을 확인해봅니다

```
$ pwd
/var/lib/docker/overlay2/2d7509079a2bfa110cdfcc26df3232690fa7b970298926a08f47fa22083b6815/diff
$ ls
```

    이제 컨테이너의 bash 셸에서 파일을 하나 만들어봅니다.

```
root@238b6c789417:/# cd tmp
root@238b6c789417:/tmp# touch THIS_IS_CONTAINER
```

    다시 docker diff 명령어로 변경된 내용을 확인해봅니다.

```
$ docker diff 238b6c789417
C /tmp
A /tmp/THIS_IS_CONTAINER
```

    앞의 문자는 변경된 내용을 의미합니다. C는 변경, A는 추가를 의미합니다. 레이어 디렉터리에서도 변경사항을 확인해보겠습니다.

```
$ pwd
/var/lib/docker/overlay2/2d7509079a2bfa110cdfcc26df3232690fa7b970298926a08f47fa22083b6815/diff
$ tree
.
└──tmp
    └──THIS_IS_CONTAINER
```

    정확히 컨테이너에서 변경된 내용만이 저장되어있는 것을 확인할 수 있습니다. 이번에는 이미지에 저장되어있는 파일을 하나 삭제해보겠습니다. 컨테이너에서 다음 명령어를 실행합니다.

```
root@238b6c789417:/# rm /bin/tar
root@238b6c789417:/# tar --version
bash: tar: command not found
```

    이 파일은 이미지 레이어에 있는 파일입니다. 이렇게 이미지 레이어에 있는 파일을 삭제하더라고 이는 컨테이너 레이어에 기록됩니다. 다시 diff 명령어로 변경된 내용을 확인해봅니다.

```
$ docker diff 238b6c789417
C /bin
D /bin/tar
C /tmp
A /tmp/THIS_IS_CONTAINER
```

    이번에는 D라는 문자열이 보입니다. 이는 파일이 삭제되었다는 의미입니다. 앞서 이야기했듯이 docker diff가 보여주는 것은 컨테이너 레이어의 변경사항이기 때문에 파일 삭제 또한 컨테이너 레이어에 기록되는 것을 확인할 수 있습니다. 실제로 컨테이너 레이어 디렉터리에서는 어떻게 저장되었을까요?

```
$ pwd
/var/lib/docker/overlay2/2d7509079a2bfa110cdfcc26df3232690fa7b970298926a08f47fa22083b6815/diff
$ tree
.
├──bin
│└──tar
└──tmp
    └──THIS_IS_CONTAINER
```

    여기는 tar 파일이 추가된 것으로 보여집니다. tree나 ls를 실행시켜보면 색이 살짝 다르게 출력될 것입니다. 이 파일은 일반 파일이 아니라 Character device 형식으로 저장되며, 이는 OverlayFS에서 파일 삭제를 나타내는 특별한 파일입니다. 이 파일은 하위 레이어의 파일이 존재하지 않는 것처럼 가려버리는 역할을 합니다.

    다른 컨테이너를 실행시켜서 nginx:latest 이미지에서 tar 파일이 삭제되지 않은 것을 확인해보겠습니다.

```
$ docker run -it nginx bash
root@46ac6042f551:/# tar --version
tar (GNU tar) 1.30
Copyright (C) 2017 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Written by John Gilmore and Jay Fenlason.
```

출처 : https://www.44bits.io/ko/post/how-docker-image-work