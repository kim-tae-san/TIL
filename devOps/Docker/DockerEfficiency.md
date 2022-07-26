# 도커 이미지 잘 만드는 방법

> 베이스 이미지는 사이즈가 작은 것을 선택

    너무나 당연한 말이지만 도커 이미지를 작게 만드는 가장 중요한 첫단계는 작은 베이스 이미지를 사용하는 것이다.

    보통 Alpine 리눅스 도커 이미지 로 시작하는 것을 추천한다.
    그 이유는 다른 리눅스 배포판과 비교해 사이즈가 훨씬 작기 때문이다. Alpine 리눅스는 용량이 작은 이유는 C 라이브러리로 musl을 채택했고, 다양한 유닉스 도구들을 탑재한 busybox를 기반으로 하고 있기 때문이다.

    하지만 Python 애플리케이션 환경에서 베이스 이미지를 선택할 때는 Alpine 리눅스를 추천하지 않는다. 

    PyPI에 올라간 파이썬 라이브러리들은 보통 Wheel 포맷을 사용하는 데 Alpine 리눅스는 Wheel 포맷을 지원하지 않기 때문에 소스 코드(.tar.gz)를 내려받아 직접 컴파일해야 하는 경우가 있기 때문이다.
    
    Wheel 포맷을 지원하지 않는 이유는 musl 라이브러리가 GNU C 라이브러리(glibc)로 컴파일된 Wheel 바이너리를 지원하지 않기 때문이다. 그래서 PyPI를 통해 .whl로 된 패키지가 아닌 소스 코드(.tar.gz)를 다운로드받게 되는 것이다.

    Wheel 포맷은 Python에서 설치 없이 바로 실행할 수 있다는 장점이 있다. 그 이유는 Wheel 포맷 자체가 zip 압축에서 확장자만 바꾼 것이며, Python은 Java의 .jar처럼 zip 파일을 바로 실행할 수 있기 때문이다.
    
    그래서 Alpine 리눅스 이미지를 사용한다면 모든 Python 패키지에서 C 코드를 컴파일 해야 하므로 도커 이미지를 빌드하는 시간이 상당히 많이 소모된다.

> 이미지 레이어 개수를 줄이자

    과거 도커 버전에서는 이미지 레이어 개수가 성능에 영향을 줬다고 한다. 하지만 이제는 그렇지 않다.

    그래도 도커 레이어 개수를 줄이는 것은 최적화 측면에서 도움이 된다고 볼 수 있다.
    레이어는 RUN, ADD, COPY 명령문에서만 생성되기 때문에 아래와 같이 여러 개로 분리된 명령을 체이닝(chaining) 으로 엮어보자.

    레이어 개수가 적다고 도커 이미지/컨테이너 성능에 영향을 주진 않지만 Dockerfile 가독성과 유지 보수 관점에서 도움이 될 것이다.
```Dockerfile
RUN apt-get update
RUN apt-get -y install git
RUN apt-get -y install locales
RUN apt-get -y install gcc
```

    위 내용을 아래와 같이 단일 RUN 구문으로 체이닝 하면 기존 4개 레이어가 생성되는 것을 1개 레이어로 줄일 수 있다. 그리고 하는 김에 설치할 패키지 순서를 알파벳 순서로 정렬하면 가독성도 높고, 중복 설치를 방지할 수 있는 효과도 있다.

```Dockerfile
RUN apt-get update && apt-get install -y \
    gcc \
    git \
    locales
```
> 애플리케이션 코드를 복사하는 부분은 아래로

    아래 샘플 Dockerfile에서는 COPY가 2번 실행된다.
    첫번째는 의존성 패키지가 명시된 파일이고, 두번째는 애플리케이션 소스 코드가 저장된 디렉터리다.

```Dockerfile
FROM python:3.8-slim-buster

WORKDIR /usr/src/app

COPY requirements.txt /usr/src/app
COPY django_project /usr/src/app

RUN pip install -r requirement.txt

CMD ["pip", "freeze"]
```

    보통 의존성 패키지는 자주 바뀌지 않기 때문에 첫번째 COPY 명령으로 생성된 레이어는 캐시 될 것이다.

    하지만 두 번째 COPY는 빌드할 때마다 바뀔 수 있기 때문에 캐시가 자주 초기화될 것이고, 그 다음 실행될 RUN명령어에서 수행하는 의존성 패키지 설치가 매번 실행될 가능성이 높다.

    이렇게 될 경우 불필요하게 빌드 시간이 늘어나고, 자칫 의존성 패키지 버전을 latest나 *로 해뒀다면 예기치 못하게 패키지 버전이 올라갈 수 있다.

    따라서 애플리케이션 코드 복사 명령은 자주 변경되지 않는 명령문 다음에 오는 것이 이미지 빌드 시간을 단축하는 데 유리하다.

> 프로그래밍 언어마다 패키지 매니저가 제공하는 Lock 파일 시스템을 활용

    이전 단락에서 의존성 패키지를 명시한 파일로 requirements.txt를 사용했다. 이 파일은 Python의 공식 패키지 관리자인 PIP에서 사용하는 관행적으로 지칭하는 파일이다.

    Python 개발 환경을 기준으로 봤을 때 PIP는 패키지별 상호 의존성에 관계를 관리할 때 부족한 면이 있다. 그래서 요즘은 좀 더 발전된 Locking 시스템을 갖춘 Pipenv나 Poetry를 사용하는 것을 추천한다.

    이 도구들을 이용해 생성된 Lock 파일 (예: Pipfile)을 기반으로 패키지가 설치될 수 있도록 한다면 위 (3) 단락에서 설명한 캐시 레이어의 장점을 얻을 수 있고, 예상치 못한 패키지 버전 업데이트도 방지할 수 있다.

```Dockerfile
FROM python:3.8-slim-buster

WORKDIR /tmp
RUN pip install pipenv

COPY Pipfile /tmp/Pipfile
COPY Pipfile.lock /tmp/Pipfile.lock

RUN pipenv install --system --deploy

CMD ["pip", "freeze"]
```

> 멀티-스테이지 빌드를 활용

    멀티-스테이지 빌드 는 위 (2), (3), (4) 단락에서 했던 노력보다 훨씬 더 효과적으로 도커 이미지 사이즈를 줄이는 방법이다.

    멀티-스테이지 빌드는 Dockerfile 1개에 FROM 구문을 여러 개 두는 방식이다.

    각 FROM 명령문을 기준으로 스테이지를 구분한다고 했을 때 특정 스테이지 빌드 과정에서 생성된 것 중 사용되지 않거나 불필요한 모든 것들을 무시하고, 필요한 부분만 가져와서 새로운 베이스 이미지에서 다시 새 이미지를 생성할 수 있다.

    아래 샘플을 통해 살펴보자.

    Pipfile에는 uwsgi라는 패키지의 의존성을 명시하고 있고, 이 패키지를 설치하기 위해서는 gcc가 필요하기 때문에 apt-get install gcc를 실행해야 한다.

    여기서 예상되는 점은 gcc가 uwsgi설치에만 관여하고 실제 애플리케이션 실행 환경에서는 필요하지 않기 때문에 멀티-스테이지 빌드를 사용한다면 이 부분이 구분점이 될 수 있겠다는 것이다.

```Dockerfile
FROM python:3.8-slim-buster AS builder

RUN apt-get update && apt-get install -y gcc

WORKDIR /tmp
RUN pip install pipenv

COPY Pipfile /tmp/Pipfile
COPY Pipfile.lock /tmp/Pipfile.lock

RUN pipenv install --system --deploy

FROM python:3.8-slim-buster
COPY --from=builder /usr/local/lib/python3.8/site-packages /usr/local/lib/python3.8/site-packages

CMD ["pip", "freeze"]
```

    COPY --from=builder를 통해 전 단계 스테이지 빌드에서 생성된 특정 결과물만 새로운 BASE 이미지로 복사해서 이미지를 생성했다.

```
REPOSITORY                          TAG     IMAGE ID       CREATED         SIZE
python-using-lockfile-multi-stage   uwsgi   7e6a652a326d   4 seconds ago   231MB
python-using-lockfile               uwsgi   17a4afc08bf7   2 minutes ago   375MB
```

    멀티-스테이지를 사용하는 것과 아닌 것의 사이즈 차이는 약 140MB 정도 발생했다.

    특별한 이유가 없다면 멀티-스테이지 빌드를 사용하지 않을 이유는 없을 것 같다.
    
출처 : https://jonnung.dev/docker/2020/04/08/optimizing-docker-images/