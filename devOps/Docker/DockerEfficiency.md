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
출처 : https://jonnung.dev/docker/2020/04/08/optimizing-docker-images/