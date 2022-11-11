# stateful? stateless? serverless? 

> 차이점

    위 세가지 형태의 서버는 데이터를 어떻게 처리하는지에 따라 분류된다. 크게는 stateful과 stateless 두 분류로 구분된다. 그러면 serverless, computerless, databaseless는 무엇일까? 

## Stateless Processing

    stateless를 설명하자면 input이외의 어떠한 데이터도 필요없는 상태를 의미한다. 

    예를들면 pdf converting 프로그램들이 있다.

    output 프로세싱은 오직 input데이터에 의해서만 정의된다. 

    이러한 타입을 side effect-free 라고 부른다. side effect에 영향을 받지 않은 연산은 pure function이라고 부른다.

<img src="./Image/stateless.png">

> pure function

    pure function은 여러가지 흥미로운 특징을 가지고 있다. 예를들면 굉장히 병렬처리하기 편하다. 50개의 파일을 손쉽게 converting할 수 있다. 각각의 파일은 서로에게 어떠한 영향을 미치지 않기 때문이다.

    또한 이것은 idempotent(멱등법칙)을 성립하는데 몇번 수행하든 결과가 달라지지 않는 성질을 가지고 있다. 상태가 없기 때문이다. 


## Stateful Processing

    stateful processing은 stateless와 대조되는 개념이다. 데이터를 처리하고싶을때는 언제나 외부데이터를 필요로 하게 된다. 
    이 경우에는 데이터를 처리할 때 이미 정의된 상태에 의해 input값을 처리한다. 이것을 side effect 라고 이야기한다.


## Serverless processing

    serverless processing은 어플리케이션의 deployment자체를 의미한다. 즉, 클라우드 컴퓨팅에 사용되는 서비스같은 의미이다. 이름에서 serverless라고 쓰여있지만 실제로 server가 없다는 의미는 아니다. 

    


출처 : https://www.reaktika.be/stateful-or-stateless-or-serverless/