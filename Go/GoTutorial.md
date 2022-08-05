# Go Module

    go module path를 로컬 경로로 변경하고싶으면 

    go mod edit -replace example.com/greetings=../greetings

    명령어 사용

변경된 mod 파일
```go
module example.com/hello

go 1.18

replace example.com/ greetings => ../greetings

```

    go mod tidy 
    명령어를 사용해서 의존성을 추가해주면 mod파일이 아래와 같이 변하게 된다.

```
module example.com/hello

go 1.16

replace example.com/greetings => ../greetings

require example.com/greetings v0.0.0-00010101000000-000000000000
```

    새로운 require필드가 생기게 되는데 이것은 example.com/hello에서 example.com/greetings 의존성을 필요로한다는것을 특정하기 위해 사용된다. 

    뒤에 추가된 번호는 pseudo-version number인데 이것은 semantic version number에 대신해서 생성된 번호이다. 


# Go Build

    코드가 있는 디렉토리로 이동. 

    $ go fmt hello.go을 실행(fmt 명령어는 필수 과정이 아니며, IDE를 사용하는 경우 자동으로 처리해준다.)

    $ go build hello.go


# Go Install

    go install은 현재 속해있는 디렉토리 코드 전체에 대한 실행 파일을 생성해준다. install이라는 뜻 그대로 설치해주는 역할을 하는데, 패키지로 사용되는 라이브러리의 경우 $GOPATH/pkg 디렉토리에 .a 확장자 파일 형태로 생성되며 cache로 사용된다. 패키지 디렉토리에서 아래의 명령어를 통해 install 할 수 있다.

