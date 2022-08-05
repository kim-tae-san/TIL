# concurrent.future 모듈

    concurrent.futures 모듈은 비동기적으로 콜러블을 실행하는 고수준 인터페이스를 제공합니다.

    비동기 실행은 (ThreadPoolExecutor를 사용해서) 스레드나 (ProcessPoolExecutor를 사용해서) 별도의 프로세스로 수행 할 수 있습니다. 둘 다 추상 Executor 클래스로 정의된 것과 같은 인터페이스를 구현합니다.

    * Executor 객체
      - ThreadPoolExecutor
      - ProcessPoolExecutor
    * Future 객체
    Thread/Process Pool 구현에 필요한 부분만 살펴보겠습니다.

## Executor 객체

    비동기적으로 호출을 실행하는 메서드를 제공하는 추상 클래스입니다. 직접 사용해서는 안 되며, 구체적인 하위 클래스를 통해 사용해야 합니다.

> ThreadPoolExecutor

    ThreadPoolExecutor 는 스레드 풀을 사용하여 호출을 비동기적으로 실행하는 Executor 서브 클래스입니다.

    Executor 객체를 이용하면 스레드 생성, 시작, 조인 같은 작업을 할 때, with 컨텍스트 관리자와 같은 방법으로 가독성 높은 코드를 구현할 수 있다.

```py
with ThreadPoolExecutor() as executor:
    future = executor.submit(함수이름, 인자)
```
> ProcessPoolExecutor

    ProcessPoolExecutor 클래스는 프로세스 풀을 사용하여 호출을 비동기적으로 실행하는 Executor 서브 클래스입니다. ProcessPoolExecutor 는 multiprocessing 모듈을 사용합니다. 전역 인터프리터 록 을 피할 수 있도록 하지만, 오직 피클 가능한 객체만 실행되고 반환될 수 있음을 의미합니다.

> multiprocessing.Pool

    multiprocessing.Pool.map을 통해 여러 개의 프로세스에 특정 함수를 매핑해서 병렬처리하도록 구현하는 방법

```py
from multiprocessing import Pool
from os import getpid

def double(i):
    print("I'm processing ", getpid())    # pool 안에서 이 메소드가 실행될 때 pid를 확인해 봅시다.
    return i * 2

with Pool() as pool:
      result = pool.map(double, [1, 2, 3, 4, 5])
      print(result)
```

    double(i)이라는 메소드가 pool을 통해 각각 다른 pid를 가진 프로세스들 위에서 multiprocess로 실행된다.

## Future 객체

    Future 클래스는 콜러블 객체의 비동기 실행을 캡슐화합니다. Future 인스턴스는 Executor.submit() 에 의해 생성됩니다.

> ProcessPoolExecutor 예제

```py
import concurrent.futures
import math

PRIMES = [
    112272535095293,
    112582705942171,
    112272535095293,
    115280095190773,
    115797848077099,
    1099726899285419]

def is_prime(n):
    if n % 2 == 0:
        return False

    sqrt_n = int(math.floor(math.sqrt(n)))
    for i in range(3, sqrt_n + 1, 2):
        if n % i == 0:
            return False
    return True

def main():
    with concurrent.futures.ProcessPoolExecutor() as executor:
        for number, prime in zip(PRIMES, executor.map(is_prime, PRIMES)):
            print('%d is prime: %s' % (number, prime))

if __name__ == '__main__':
    main()
```



출처 : https://velog.io/@cha-suyeon/%ED%8C%8C%EC%9D%B4%EC%8D%AC%EC%97%90%EC%84%9C-%EC%8A%A4%EB%A0%88%EB%93%9C%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%ED%92%80-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0