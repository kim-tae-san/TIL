# Decorator

```py
def decorator_function(original_function):  # 1
    def wrapper_function():  # 5
        return original_function()  # 7

    return wrapper_function  # 6


def display():  # 2
    print('display 함수가 실행됐습니다.')  # 8


decorated_display = decorator_function(display)  # 3

decorated_display()  # 4
```

    위의 코드는 다음과 같은 내용입니다.  데코레이터 함수인 decorator_function과 일반 함수인 display를 #1과 #2에서 각각 정의를 하였습니다. 그 다음에 #3에서 decorated_display라는 변수에 display 함수를 인자로 갖은 decorator_function을 실행한 리턴값을 할당하였습니다. 물론 이 리턴값은 wrapper_function이 되겠죠. 여기서 wrapper_function 함수는 아직 실행된게 아닙니다. decorated_display 변수 안에서 호출되기를 기다리는 겁니다. 그리고 #4의 decorated_display()를 통해 wrapper_function을 호출하면 #5번에서 정의된  wrapper_function이 호출이 됩니다. 그러면 #7에서 original_function인 display 함수가 호출되어 #8의 print 함수가 호출되고 문자열이 출력되는 겁니다.

    그런데 이 복잡한 데코레이터라는 것을 도데체 왜 쓰는걸까요? 그 이유는 강좌의 도입부에서도 설명을 드렸듯이 이미 만들어져 있는 기존의 코드를 수정하지 않고도, 래퍼(wrapper) 함수를 이용하여 여러가지 기능을 추가할 수가 있기 때문입니다. 예제를 볼까요?


```py
def decorator_function(original_function):
    def wrapper_function():
        print('{} 함수가 호출되기전 입니다.'.format(original_function.__name__))
        return original_function()

    return wrapper_function


def display_1():
    print('display_1 함수가 실행됐습니다.')


def display_2():
    print('display_2 함수가 실행됐습니다.')


display_1 = decorator_function(display_1)  # 1
display_2 = decorator_function(display_2)  # 2

display_1()
print()
display_2()
```

    display_1 함수가 호출되기전 입니다.
    display_1 함수가 실행됐습니다.

    display_2 함수가 호출되기전 입니다.
    display_2 함수가 실행됐습니다.

위의 예제와 같이 하나의 데코레이터 함수를 만들어 display_1과 display_2, 두 개의 함수에 기능을 추가할 수가 있는겁니다.그런데 일반적으로 위의 예제의 #1, #2와 같은 구문은 잘 사용하지 않고, “@” 심볼과 데코레이터 함수의 이름을 붙여 쓰는 간단한 구문을 사용합니다. 위의 코드는 다음의 코드와 같이 간소화할 수가 있습니다.

```py
def decorator_function(original_function):
    def wrapper_function():
        print('{} 함수가 호출되기전 입니다.'.format(original_function.__name__))
        return original_function()

    return wrapper_function


@decorator_function  # 1
def display_1():
    print('display_1 함수가 실행됐습니다.')


@decorator_function  # 2
def display_2():
    print('display_2 함수가 실행됐습니다.')


# display_1 = decorator_function(display_1)  # 3
# display_2 = decorator_function(display_2)  # 4

display_1()
print()
display_2()
```

    그런데 다음 코드의 display_info 함수와 같이 인수를 가진 함수를 데코레이팅하고 싶을 때는 어떻게 할까요? 파일을 수정하고 실행을 해보죠.

```py
def decorator_function(original_function):
    def wrapper_function():
        print('{} 함수가 호출되기전 입니다.'.format(original_function.__name__))
        return original_function()
    return wrapper_function


@decorator_function
def display():
    print('display 함수가 실행됐습니다.')


@decorator_function
def display_info(name, age):
    print('display_info({}, {}) 함수가 실행됐습니다.'.format(name, age))


display()
print()
display_info('John', 25)
```

    display 함수가 호출되기전 입니다.
    display 함수가 실행됐습니다.

    Traceback (most recent call last):
    File "decorator.py", line 21, in <module>
        display_info('John', 25)
    TypeError: wrapper_function() takes 0 positional arguments but 2 were given

wrapper_function은 인자를 받지 않는데 2개의 인자가 전달되었다는 타입에러가 발생했습니다. 다음과 같이 코드를 수정하면 문제가 해결됩니다.

```py
def decorator_function(original_function):
def wrapper_function(*args, **kwargs):  #1
    print('{} 함수가 호출되기전 입니다.'.format(original_function.__name__))
    return original_function(*args, **kwargs)  #2
return wrapper_function


@decorator_function
def display():
    print('display 함수가 실행됐습니다.')


@decorator_function
def display_info(name, age):
    print('display_info({}, {}) 함수가 실행됐습니다.'.format(name, age))


display()
print()
display_info('John', 25)
```

    display 함수가 호출되기전 입니다.
    display 함수가 실행됐습니다.

    display_info 함수가 호출되기전 입니다.
    display_info(John, 25) 함수가 실행됐습니다.

#1과 #2에 인수를 추가하여 문제를 해결하였습니다.

데코레이터는 이런 함수 형식 말고도 클래스 형식을 사용할 수도 있습니다. 한번 구경해 볼까요?

```py
# def decorator_function(original_function):
#     def wrapper_function(*args, **kwargs):
#         print '{} 함수가 호출되기전 입니다.'.format(original_function.__name__)
#         return original_function(*args, **kwargs)
#     return wrapper_function


class DecoratorClass:  # 1
    def __init__(self, original_function):
        self.original_function = original_function

    def __call__(self, *args, **kwargs):
        print('{} 함수가 호출되기전 입니다.'.format(self.original_function.__name__))
        return self.original_function(*args, **kwargs)


@DecoratorClass  # 2
def display():
    print('display 함수가 실행됐습니다.')


@DecoratorClass  # 3
def display_info(name, age):
    print('display_info({}, {}) 함수가 실행됐습니다.'.format(name, age))


display()
print()
display_info('John', 25)
```

    display 함수가 호출되기전 입니다.
    display 함수가 실행됐습니다.

    display_info 함수가 호출되기전 입니다.
    display_info(John, 25) 함수가 실행됐습니다.

#1에서 DecoratorClass를 정의한 뒤에 #2, #3에서 @DecoratorClass로 변경하니 decorator_function을 사용한 것과 똑같은 결과가 출력된 것을 볼 수 있습니다. 하지만 클래스 형식의 데코레이터는 그다지 많이 사용되지 않고, 보통 함수 형식이 많이 사용됩니다. 그냥 클래스 형식도 있다는 것 정도만 알아두시면 될 것 같습니다.