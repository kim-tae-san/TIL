# Function

    go에서 함수는 func 키워드를 사용하여 정리한다.
    
    호출되는 함수가 반드시 호출하는 함수의 앞에서 정의되어야 할 필요는 없다.


    포인터가 있는 곳에 reference가 있는 법

    Go에서도 함수에 인자를 전달하는 방법이 Pass by Value와 Pass by Ref 로 나뉜다.

    늘 그렇듯이 Pass By Value는 인자의 값을 복사하여 함수에 전달하고,

    Pass By Reference는 인자가 저장된 주소값을 가져와서 dereference하여 사용한다.


    다른 언어에선 모르겠지만, C++과 확연하게 다른 점이 함수파트에서 극명하게 드러난다.

> Variadic Function (가변인자함수)
    
    함수에 정해진 수의 인자가 아닌 N개 (N은 0이상의 정수) 의 인자를 전달하고 싶을 때가 있다.

    이럴 때 Go에서는 ellipsis를 통해 그것을 가능케 한다. 

 
```go
package main
func main() {   
    say("This", "is", "a", "book")
    say("Hi")
}
 
func say(msg ...string) {
    for _, s := range msg {
        println(s)
    }
}
 ```
    If the last parameter of a function has type ...T, it can be called with any number of trailing arguments of type T.

    The actual type of ...T inside the function is []T.

그러니까 결국 마지막 param의 type앞에 ...이 붙으면 N(N>=0)개의 인자가 들어갈 수 있다.

> Anonymous Function

    익명함수란, 함수명을 갖지 않는 함수를 말한다. function literal 이라고도 한다.

    보통 함수 전체가 변수에 할당되거나, 다른 함수의 parameter에 직접 정의되곤 한다.

 
```go
package main
 
func main() {
    sum := func(n ...int) int { //익명함수 정의
        s := 0
        for _, i := range n {
            s += i
        }
        return s
    }
 
    result := sum(1, 2, 3, 4, 5) //익명함수 호출
    println(result)
}
```

# Function as Type

함수의 원형을 하나의 type으로 만들 수 있다.

이렇게 함수의 원형을 정의하고 함수를 타 메서드에 전달하고 리턴받는 기능을 타 언어에서 흔히 델리게이트(Delegate)라 부른다. Go는 이러한 Delegate 기능을 제공하고 있다.

```go
// 원형 정의
type calculator func(int, int) int
 
// calculator 원형 사용
func calc(f calculator, a int, b int) int {
    result := f(a, b)
    return result
}
```

# Closure

Closure
함수 바깥에 있는 변수를 참조하는 function value. 함수 밖의 변수를 함수 안에서 읽고 쓸 수 있다.

Go에서 함수는 Closure로 사용될 수 있다.

 

다음 예제를 일단 한 번 보자

 
```go
package main

func nextValue() func() int {
    i := 0
    return func() int {
        i++
        return i
    }
}

func main() {
    next := nextValue()
 
    println(next())  // 1
    println(next())  // 2
    println(next())  // 3
 
    anotherNext := nextValue()
    println(anotherNext()) // 1 다시 시작
    println(anotherNext()) // 2
}
 ```

closure를 사용하면 지역 변수가 소멸되지 않고, 함수가 호출될 때마다 계속 그 변수를 사용할 수 있다.

함수가 선언될 때의 환경을 유지하여, 프로그램의 흐름을 변수에 저장할 수 있게한다.