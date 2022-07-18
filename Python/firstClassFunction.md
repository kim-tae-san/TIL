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