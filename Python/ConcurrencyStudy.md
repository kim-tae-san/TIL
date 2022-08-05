# Python Concurrency

> Abstract

    파이썬에서 멀티스레딩으로 공유자원을 읽고 쓸때 발생하는 일에 대해 설명한다.

    대부분의 프로그램은 공유자원을 보호하기위해 lock을 사용하는데 이 문서에서는 파이썬에서 락을 사용하지 않았을 경우에 발생하는 이상한 일들에 대해 조명하도록 한다. 

> Raionale

    Python에는 크게 4종류의 Implementation이 있다. - CPython, Jython, IronPython, PyPy. 이중 몇개는 이미 여러 플랫폼들에서 실행되고 있고 엄청난 경량화를 이루었다. 보통, 이런 경량화같은 경우는 싱글 스레드 작업으로는 잘 보이지 않는다. 하지만 concurrency 환경에서 멀티 스레드로 실행하면 잘 볼 수 있다. CPython의 경우는 현재 GIL(Global Interpreter Lock)을 사용하고 있어서 각 스레드들이 예상한 결과를 볼 수 있도록 보장한다. 그러나 이 방법은 싱글 프로세서에 한정한다. 
    Jython이나 IronPython은 각각 자바와 .NET의 스레딩 시스템을 차용한다. 이건 CPython보다 더 많은 코어를 사용하는 이점을 쥘 수 있지만 공유자원에 대한 스레드간의 충돌이 일어날 수 있다.

> Two Simple memory models

    Sequenctial Consistency
        