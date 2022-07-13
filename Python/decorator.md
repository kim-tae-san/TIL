# 퍼스트 클래스 함수

> 퍼스트 클래스란? 

    * 프로그래밍 언어가 함수를 first-class-citizen으로 취급하는 것을 말합니다.

    * 함수 자체를 인자로써 다른 함수에 전달하거나 다른 함수의 결과값으로 리턴 할 수도 있고, 함수를 변수에 할당하거나 데이터 구조 안에 저장할 수 있는 함수를 뜻합니다.

> 퍼스트 클래스 함수 예시
```py
def square(x):
    return x * x

def cube(x):
    return x * x * x

def quad(x):
    return x * x * x * x

def my_map(func, arg_list):
    result = []
    for i in arg_list:
        result.append(func(i))  # square 함수 호출, func == square
    return result

num_list = [1, 2, 3, 4, 5]

squares = my_map(square, num_list)
cubes   = my_map(cube, num_list)
quads   = my_map(quad, num_list)

print(squares)
print(cubes)
print(quads)

#
[1, 4, 9, 16, 25]
[1, 8, 27, 64, 125]
[1, 16, 81, 256, 625]
```

# 클로저(Closure)

    프로그래밍 언어에서의 클로저란 퍼스트클래스 함수를 지원하는 언어의 네임 바인딩 기술이다. 클로저는 어떤 함수를 함수 자신이 가지고 있는 환경과 함께 저장한 레코드이다. 또한 함수가 가진 프리변수(free variable)를 클로저가 만들어지는 당시의 값과 레퍼런스에 맵핑하여 주는 역할을 한다. 클로저는 일반 함수와는 다르게, 자신의 영역 밖에서 호출된 함수의 변수값과 레퍼런스를 복사하고 저장한 뒤, 이 캡처한 값들에 액세스할 수 있게 도와준다.

> 프리변수(free variable)란?

    파이썬에서 프리변수는 코드블럭안에서 사용은 되었지만, 그 코드블럭안에서 정의되지 않은 변수를 뜻합니다.
    
https://schoolofweb.net/blog/posts/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0-decorator/