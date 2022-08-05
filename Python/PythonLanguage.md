# Python Implementation

    Python은 언어를 칭하기도 하지만 동시에 Python 언어가 실행될 수 있는 환경이 구축된 소프트웨어 또는 시스템을 말한다. 이런 시스템을 Python Implementation이라고 한다. Python Implementation은 여러 언어 환경에서 가능하다. ‘The Python Language Reference (release 3.9.7) 에서는 아래와 같이 크게 5가지 구현체를 소개하고 있다.

> CPython

    파이썬의 창시자 귀도 반 로섬(Guido van Rossum)이 만든 표준 파이썬 구현으로 C로 작성되었다. 다만 다른 구현체와 구분하기 위하여 CPython이라고 부른다. 다시 말해 Python은 본래 Cpython에 의해 처리, 실행되는 언어이다.

    Python은 플랫폼 독립적이며 인터프리터식, 객체지향적, 동적 타이핑을 지원하는 대화형 언어이다. CPython은 인터프리터이면서 컴파일러이다. 작성된 python 소스 코드를 바이트코드로 컴파일한 후 가상머신의 인터프리터가 실행한다.

    .py파일을 실행하면 .pyc라는 파일이 생성되는데 이 파일에 Cpython이 컴파일한 바이트코드가 들어있다. 파이썬 쉘에서 컴파일하고 싶다면 ‘import py_compile / py_compile.compile(‘~.py’)를 사용하고, OS 쉘에서 컴파일하고 싶다면 ‘python -m py_compile ~.py’를 사용한다. Dis 패키지를 이용하여 소스코드가 어떻게 바이트코드로 변환되는지 직접 확인할 수 있다.
    
    Python의 인터프리터 사용은 언어가 느리다고 생각되는 원인이 되며, Duck typing 과 같은 런타임 시 객제의 변수 및 메소드 집합이 객체의 타입을 결정하는 동적 타이핑 방식의 유연함은 스스로 버그를 찾는 능력이 부족한 초보 프로그래머들에게 오히려 독이 되기도 한다.

    Cpython 인터프리터는 GIL(Global Interpreter lock)을 사용한다. C로 구현된 라이브러리 적용을 간단하게 하기 위해서이다. C 라이브러리 함수 호출이 빠른 것은 Python의 큰 장점이다. 모든 객체는 자신을 가리키는 reference를 count 하는 변수(reference count variable)이 존재하며 count가 0이 될 때 메모리가 해제된다. GIL의 실행단위는 바이트 코드이며 python에서 바이트코드를 실행하기 위해서는 interpreter lock을 먼저 얻어야 한다. 바이트코드를 실행할 때 여러 thread를 사용할 경우, 전체에 lock을 걸어서 한 번에 단 하나의 thread만이 python object에 접근하도록 제한한다(GIL is a mutex(or lock) that allows only one thread to hold the control of the Python interpreter.).
    
    GIL은 race condition으로부터 발생 가능한 memory leak, reference가 존재하는데도 메모리가 할당되는 문제를 막고 reference count variable을 지켜내면서 thread safety를 유지한다. 다만 참조 회수가 0은 아니지만 도달 수 없는 상태인 reference cycles(순환 참조)가 발생했을 때는 garbage collection으로 그 상황을 해결한다.
    
    다만 이 GIL thread safe memory management는 단점도 있다. I/O bound 환경에서는 높은 성능을 내지만 CPU-bound 환경에서는 멀티 코어 시스템의 이점을 누리지 못하고 멀티 스레드 환경에서 bottleneck 현상이 발생할 수 있다. 그러나 GIL을 제거하면 C extenstions에 문제가 생기고 오히려 향상되었던 성능이 저하된다는 문제가 있다. Lock을 얻고 해제하는 과정에서 overhead가 발생하는 멀티 스레드 환경에서는 GIL 대신 멀티프로세싱이나 ProcessPoolExecutor를 사용하기도 한다.


> Jython

    Java로 작성된 파이썬 구현체이며 JVM 위에서 실행된다. 공식 웹사이트(
    www.jython.org) 에서 파일을 다운 받아 설치 가능하다. Jython을 실행하기 위해서 윈도우에서는 jpython.bat, 유닉스/리눅스/맥에서는 jython.sh을 이용하면 된다. 패키지를 불러와서 간단하게 필요한 Java 클래스와 메소드를 호출할 수 있다.
    Jython은 스크립팅 언어로서 대화형 언어이기 때문에 Java에서와 같이 사전 컴파일 단계가 없다. Jython 어플리케이션을 위하여 작성된 python 코드는 최종적으로 프로그램이 실행될 때 Java 바이트코드로 컴파일 된다. 이러한 파일은 jython.jar 에 미리 컴파일되어 있다.
    Python은 모든 데이터 및 코드를 포함한 모든 것을 오브젝트로 본다. 그러므로 코드 행으로 이들 오브젝트를 조작할 수 있다. 숫자 및 문자열 같은 일부 선택 유형은 오브젝트가 아니라 값으로 더 편리하게 간주된다. 지원되는 하나의 Null 값은 None의 예약 이름을 갖습니다.
    Jython 런타임 시스템에 알려진 모든 객체는 클래스의 인스턴스 PyObject 또는 해당 하위 클래스 중 하나로 표시된다. (java.lang.object -> org.python.core.PyObject) Python의 함수는 call 메소드를 지원하는 PyObject라는 개체의 인스턴스이며 추가적인 인트로스펙션이 사용 가능하다. 일반적으로 어떤 함수가 Python으로 작성된 표준 함수라면 그것은 PyFunction 클래스에 속하며 내장 함수는 PyBuiltinFunction 클래스에 속한다. 그러나 다른 많은 개체들도 call 함수 인터페이스를 지원하며 여러 단계에 거쳐 대리(proxy) 되었을 가능성이 있기 때문에 함수가 PyObject가 일 것이라고 가정하는 것 외에 구체적인 것은 속단할 수 없다.

> Python for .NET

    Python.NET( pythonnet)은 Python 프로그래머에게 Windows의 .NET 4.0+ CLR(공용 언어 런타임) 및 Linux 및 OSX의 Mono 런타임과 거의 완벽하게 통합되는 패키지이다. Python.NET은 .NET 개발자를 위한 애플리케이션 스크립팅 도구를 제공한다. 이 패키지를 사용하면 CLR(C#, VB.NET, F#, C++/CLI)을 대상으로 하는 모든 언어로 작성된 .NET 서비스 및 구성 요소를 사용하여 .NET 응용 프로그램을 스크립팅하거나 Python에서 전체 응용 프로그램을 빌드할 수 있다.
    바로 다음에서 다루는 IronPython과의 가장 큰 차이는 표준 CPython 런타임을 사용하며 이 패키지는 python CLR로 번역하지 않으며 IL도 생성하지 않는다. 오히려 Cpython 엔진과 .NET 또는 Mono 런타임의 통합에 가깝다. (다만 매니지드 코드이다.) 이 접근 방식을 사용하면 python 코드의 기본 실행 속도를 유지하면서 CLR 서비스를 사용하고 기존 python 코드 및 C-API 확장을 계속 사용할 수 있다. 현재 릴리스는 python.net 웹사이트에서 사용할 수 있다.

> IronPython

    IronPython은 Microsoft의 CLR(Common Language Runtime)팀이 작성한 Python 구현이다. .NET 프레임워크에서 실행되며 완전한 동적 컴파일을 통해 대화형 콘솔을 지원한다.
    C# 언어로 작성된 파이썬 구현체로 IL Generator에 의해 MSIL(Microsoft Intermediate Language)로 생성되며, .NET으로 바로 컴파일되어 실행되는 Python이다. Cpython에는 바이트 코드를 해석하는 런타임 루프가 있지만, IronPython 바이트 코드는 CLR의 중간 언어인 MSIL로이며 결국 네이티브 코드로 변환된다.
    따라서 Python for .NET와 유사하지만 .NET 프레임워크에 훨씬 네이티브하며 Python 프로그래머가 모든 .NET 라이브러리를 쉽게 사용할 수 있다. 따라서 .NET의 다른 언어와 통합해서 작업을 해야 할 때 IronPython에 비해 더 유용하다.

> PyPy

    Python 언어(RPython)으로 작성된 구현체로 JIT 컴파일러 방식으로 구현하여 기존의 interpreter 방식보다 훨씬 빠르고 효율적이다. 다만 JIT 컴파일러의 효율을 극대화하려면 실행 프로세스가 최소 몇 초 이상 유지되어야 하며, 실행 시간이 대부분 C 런타임 라이브러리인 경우 JIT는 큰 역할을 하기 힘들다. 따라서 PyPy가 유리한 상황은 코드를 실행하는 데 상당한 시간이 소요되는 장시 실행 프로그램을 실행할 때이다. 또한 PyPy는 madvise() 시스템이 호출될 때만 MADV_FREE로 마크된 사용하지 않은 메모리를 운영체제에 반환하므로 기본적으로 Cpython보다 메모리를 많이 사용하지만 아주 큰 프로그램에서는 Cpython보다 메모리를 덜 사용한다.
    Python으로 작성되었기 때문에 인터프리터 수정과 이 쉽고 언어 자체에 대한 실험을 장려하는 것이 프로젝트의 주요 목적 중 하나이다. 대표적인 예로 스레드 기반 프로그래밍의 이점을 누리기 위해 도입된 Stackless python를 도입했다.
    PyPy는 Cpython과 다르게 참조 계산을 기반으로 GC(Gabage Collection)을 해결하지 않는다. 즉 객체에 더 이상 연결할 수 없을 때 즉시 해제되지 않고 범위를 벗어나도 즉시 닫히는 문제가 없다. PyPy2와 Python3에서 프로그램 끝에 gc.collect() 호출을 추가하면 garbage collector가 닫는 모든 파일과 소켓에 대해 ResourceWarning이 생성된다. 이 경고는 파일이나 소켓이 생성된 위치의 스택을 추적하므로 프로그램의 어떤 부분이 파일을 명시적으로 닫지 않는지 쉽게 확인할 수 있다.
    그러나 PyPy도 여전히 GIL을 사용한다. GIL 없이 작동하는 대체 PyPy를 만들기 위해 STM(Software Transactional Memory) 버전을 지원하는 작업이 있었으나 기술적 어려움으로 지연되고 있다.