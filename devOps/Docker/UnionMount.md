# Union Mount with OverlayFS

> Union Mount File System

    유니온 마운트란, 하나의 디렉터리 지점에 여러 개의 디렉터리를 마운트함으로써, 마치 하나의 통합된 디렉터리처럼 보이게 하는 것을 의미한다. 이를 간단하게 설명하자면, 마치 투명한 셀로판지를 여러 개 겹쳐서 위에서 내려다 보는 것과 비슷하다고 할 수 있다. 겹쳐진 여러 개의 셀로판지를 위에서 보게 되면 가장 위쪽에 있는 셀로판지의 그림만이 보일 것이고, 아래의 셀로판지 그림은 위쪽에 위치한 셀로판지에 의해 가려져서 보이지 않기 때문이다. 


# OverlayFS 사용하기

    도커 이미지를 제대로 이해하려면 결국 OverayFS 혹은 유니온 마운트 개념을 이해할 필요가 있습니다. 유니온 마운트가 오늘의 메인 주제는 아닙니다만, 이야기가 나온 김에 조금만 더 자세히 알아보겠습니다.

    OverlayFS를 사용하는 아주 간단한 예를 살펴보고, 직접 위의 컨테이너와 같은 파일 시스템을 구축해보겠습니다

```
$ mkdir overlayfs; cd overlayfs
$ mkdir container image1 image2 merge work
$ touch image1/a image1/b image2/c
$ sudo mount -t overlay overlay -o lowerdir=image2:image1,upperdir=container,workdir=work merge
```    

    먼저 5개의 디렉터리를 만들었습니다. work는 직접 사용하지 않으므로, work 디렉터리가 필요하다는 정도만 기억해두시면 됩니다. 실제로 데이터가 들어가는 디렉터리는 contanier와 image1, image2입니다. 구성하고자 하는 디렉터리(레이어) 계층은 다음과 같습니다.

```
container(최상위) = upperdir
image2
image1
```

    container는 최상위 디렉터리가 됩니다. OverlayFS에서 최상위 디렉터리는 upperdir로 나타냅니다. 나머지 하위 데이터 디렉터리는 모두 lowerdir에 포함됩니다. lowerdir은 :로 구분되며 맨 뒤에서부터 앞으로 쌓아나갑니다. 따라서 위와 같은 구조는 다음과 같이 표현됨니다: lowerdir=image2:image1. merge 디렉터리는 모든 계층을 쌓여서(겹쳐서) 보여지는 디렉터리입니다. 실제 작업은 이 merge 디렉터리에서 이루어지고, 변경사항은 upperdir에 저장됩니다. 이와 같은 내용을 참고해서 mount 명령을 실행하면 준비는 끝이 납니다.

    이제 실제로 잘 적용이 되었는지 확인해보겠습니다.

```
$ tree . -I work
.
├──container
├──image1
│├──a
│└──b
├──image2
│└──c
└──merge
    ├──a
    ├──b
    └──c
```

    이미지들의 파일들이 merge 디렉터리에 합쳐서 보여지는 것을 알 수 있습니다. 컨테이너와 마찬가지로 최상위 레이어인 container는 비어있습니다. merge 디렉터리에 파일을 변경하면, 모든 변경사항은 upperdir인 container 디렉터리에 저장됩니다. merge 디렉터리에서 a 파일을 삭제하고 d 파일을 추가해보겠습니다.

```
$ rm ./merge/a
$ touch ./merge/d
$ tree . -I work
.
├──container
│├──a
│└──d
├──image1
│├──a
│└──b
├──image2
│└──c
└──merge
    ├──b
    ├──c
    └──d
```
    
    변경이 일어나기 전과 비교해보시기 바랍니다. merge 디렉터리에서는 정확히 우리가 의도한 대로 a가 삭제되었고, d가 추가되었습니다. 그리고 우선 container 디렉터리에 d 파일이 추가된 것을 확인할 수 있습니다. 그리고 재미있게도 container 디렉터리에 a라는 파일도 추가되어있네요. 이는 앞에서 잠깐 설명했었는데 Character device라는 특수한 형식의 파일로 삭제된 파일을 의미합니다. 그리고 image1과 image2에는 아무런 변화도 없습니다.


출처:https://blog.naver.com/alice_k106/221530340759

출처:https://www.44bits.io/ko/post/how-docker-image-work#%ED%92%80-%EB%B0%9B%EC%9D%80-%EB%8F%84%EC%BB%A4-%EC%9D%B4%EB%AF%B8%EC%A7%80%EB%8A%94-%EC%96%B4%EB%94%94%EC%97%90-%EC%A0%80%EC%9E%A5%EB%90%98%EB%82%98%EC%9A%94