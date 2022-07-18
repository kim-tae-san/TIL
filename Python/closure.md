# 클로저(Closure)

    프로그래밍 언어에서의 클로저란 퍼스트클래스 함수를 지원하는 언어의 네임 바인딩 기술이다. 클로저는 어떤 함수를 함수 자신이 가지고 있는 환경과 함께 저장한 레코드이다. 또한 함수가 가진 프리변수(free variable)를 클로저가 만들어지는 당시의 값과 레퍼런스에 맵핑하여 주는 역할을 한다. 클로저는 일반 함수와는 다르게, 자신의 영역 밖에서 호출된 함수의 변수값과 레퍼런스를 복사하고 저장한 뒤, 이 캡처한 값들에 액세스할 수 있게 도와준다.

> 프리변수(free variable)란?

    파이썬에서 프리변수는 코드블럭안에서 사용은 되었지만, 그 코드블럭안에서 정의되지 않은 변수를 뜻합니다.

> 클로저 예시

```py
def outer_func(): # 1
    message = 'Hi' # 3

    def inner_func(): # 4
        print message # 6 

    return inner_func() # 5

outer_func() # 2
```

    #1에서 정의된 함수 outer_func를 #2에서 호출을 합니다. 물론 outer_func는 어떤 인수도 받지 않습니다.

    outer_func가 실행된 후, 가장 먼저 하는 것은 messge 라는 변수에 ‘Hi’라는 문자열을 할당합니다. #3입니다.

    #4번에서 inner_func를 정의하고 #5번에서 inner_func를 호출하며 동시에 리턴합니다.

    #6에서 message 변수를 참조하여 출력합니다. 여기서 message는 inner_func 안에서 정의되지는 않았지만, inner_func 안에서 사용되기 때문에 프리변수라고 부릅니다.

“Hi”가 출력되기까지의 일련의 프로세스는 위와 같습니다. 여기까지는 이해하기 어려운 점이 하나도 없습니다. 그럼 다음 단계로 넘어가 보죠.

```py
def outer_func(): # 1
    message = 'Hi' # 3

    def inner_func(): # 4
        print(message)  # 6

    return inner_func # 5

my_func = outer_func() # 2

my_func() # 7
my_func() # 8
my_func() # 9
```

    Hi
    Hi
    Hi
my_func()를 3번 실행하여 “Hi”를 3번 출력하였습니다. 그런데 뭔가 이상합니다. outer_func는 분명히 #2에서 호출된 후, 종료되었습니다. 그런데 #7, #8, #9에서 호출된 my_func(*여기서 my_func는 inner_func와 같다는 점을 기억하시기 바랍니다.) 함수가 outer_func 함수의 로컬변수인 message를 참조했다는 겁니다. 흠… 신기하네요… 어떻게 가능했을까요? 그 해답은 클로저입니다. 클로저는 함수의 프리변수 값을 어딘가에 저장한다고 했던것 같습니다. 도대체 어디에 저장을 하는 걸까요? 같이 그 비밀을 파해쳐 보시죠. ㅋ

일단 코드를 다음과 같이 수정한 후, 실행하여 보죠.

```py
 def outer_func():  # 1
    message = 'Hi'  # 3

    def inner_func():  # 4
        print(message)  # 6

    return inner_func  # 5

my_func = outer_func()  # 2

print(my_func)  # 7
print()
print(dir(my_func))  # 8
print()
print(type(my_func.__closure__))  # 9
print()
print(my_func.__closure__)  # 10
print()
print(my_func.__closure__[0])  # 11
print()
print(dir(my_func.__closure__[0]))  # 12
print()
print(my_func.__closure__[0].cell_contents)  # 13
```

    <function outer_func.<locals>.inner_func at 0x000002913914AA60>

    ['__annotations__', '__call__', '__class__', '__closure__', '__code__', '__defaults__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__get__', '__getattribute__', '__globals__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__kwdefaults__', '__le__', '__lt__', '__module__', '__name__', '__ne__', '__new__', '__qualname__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__']

    <class 'tuple'>

    (<cell at 0x000002913910B730: str object at 0x0000029139147B70>,)

    <cell at 0x000002913910B730: str object at 0x0000029139147B70>

    ['__class__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__ne__', '__new__', '__reduce__', '__reduce_ex__','__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 'cell_contents']

    Hi

    출력된 결과를 하나씩 확인하겠습니다.

    #7 결과: my_func 변수에 inner_func 함수 오브젝트가 할당되어 있는 것은 위에서도 확인을 하였습니다.

    #8 결과: 클로저가 도데체 어디에 데이터를 숨기는지 dir() 명령어를 이용해 my_func의 네임스페이스를 확인해 봤습니다. 아하! __closure__ 라는 속성이 숨어 있는 것을 알았습니다.

    #9 결과: __closure__는 무슨 타입인지 확인해 봤습니다. 타입은 튜플이라고 하네요.

    #10 결과: 그 튜플안에 뭐가 들어 있는지 확인해 봤습니다. 아이템이 하나 들어있네요.

    #11 결과: 튜플안에 첫 번째 아이템입니다. “cell”이라는 문자열 오브젝트네요.

    #12 결과: 이 cell 오브젝트는 어떤 속성들을 가지고 있을까요? 여기에 뭔가 답이 있을 것 같습니다. “cell_contents”라는 속성이 있네요.

    #13 결과: cell_contents에는 뭐가 들어 있는지 확인 해 보겠습니다. ‘Hi’가 들어 있네요. 🙂

    드디어 클로저가 어디에 데이터를 저장하는지 힘들게 알아냈습니다. ^^;;

    여기서 부터 함수의 파라메터를 사용하면 더욱 더 재밌는 코드를 만들 수가 있습니다. 다음의 코드를 저장하고 실행해 봅시다.

```py
def outer_func(tag):  # 1
    text = 'Some text'  # 5
    tag = tag  #6

    def inner_func():  # 7
        print('<{0}>{1}<{0}>'.format(tag, text))  # 9

    return inner_func  # 8

h1_func = outer_func('h1')  # 2
p_func = outer_func('p')  # 3

h1_func()  # 4
p_func()  # 10
```

    <h1>Some text<h1>
    <p>Some text<p>
outer_func 함수 하나로 h1태그와 p태그로 문자열을 감싸는 함수를 만들어 봤습니다. 이번에는 태그안의 문자열을 컨트롤할 수 있는 함수를 만들어 봅시다

```py
def outer_func(tag):  # 1
    tag = tag  # 5

    def inner_func(txt):  # 6
        text = txt  # 8
        print('<{0}>{1}<{0}>'.format(tag, text))  # 9

    return inner_func  # 7

h1_func = outer_func('h1')  # 2
p_func = outer_func('p')  # 3

h1_func('h1 태그의 안입니다.')  # 4
p_func('p 태그의 안입니다.')  # 10
```

    <h1>h1 태그의 안입니다.<h1>
    <p>p 태그의 안입니다.<p>

클로저는 이렇게 하나의 함수로 여러가지의 함수를 간단히 만들어낼 수 있게도 해주며, 기존에 만들어진 함수나 모듈등을 수정하지 않고도 wrapper 함수를 이용해 커스터마이징할 수 있게 해주는 기특한 녀석입니다.