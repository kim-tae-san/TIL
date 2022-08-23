# Pipeline

```jenkinsfile
pipeline {
  agent any
  stages {
    stage('git scm update') {
      steps {
        git url: 'https://github.com/IaC-Source/echo-ip.git', branch: 'main'
      }
    }
    stage('docker build and push') {
      steps {
        sh '''
        docker build -t 192.168.1.10:8443/echo-ip .
        docker push 192.168.1.10:8443/echo-ip
        '''
      }
    }
    stage('deploy kubernetes') {
      steps {
        sh '''
        kubectl create deployment pl-bulk-prod --image=192.168.1.10:8443/echo-ip
        kubectl expose deployment pl-bulk-prod --type=LoadBalancer --port=8080 \
                                               --target-port=80 --name=pl-bulk-prod-svc
        '''
      }
    }
  }
}
```

위와 같이 jenkinsfile을 이용해서 pipeline구축을 할 수 있다.

> pipeline

선언적인 문법이 시작되는 부분이다. {} 안에 내용을 작성하면 된다.

> agent

작업을 수행할 에이전트를 지정하고 필요한 설정을 한다. 

에이전트를 지정할때 any, label, docker, kubernetes와 같은것들이 있는데

any의 경우는 에이전트를 젠킨스가 임의로 지정하는 것이다.

label의 경우는 특정 레이블과 일치하는 에이전트 노드를 지정한다.

docker의 경우는 에이전트 노드의 이미지를 도커로 지정한다.

kubernetes의 경우는 에이전트 노드를 쿠버네티스 파드로 지정한다.

> stages

stages의 경우는 stage를 모아서 정의하고 순서대로 진행하도록 하는 역할을 한다.

> stage
step들을 정의하는 영역이다. stage 괄호 내에 여러개의 step을 정의할 수 있고 이때 step에서 실제 내부에서 동작하는 내용이 정의된다.


