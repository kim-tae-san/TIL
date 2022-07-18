#Data Type

> String

    Back Quote(``)와 Double Quote("")로 선언할 수 있다. (single quote ('') 안 씀 )

    Back Quote로 쓰인 문자열은 Raw String Literal 로서, 문자열이 별도로 해석되지 않는다. 예를들어, '\n'은  new line으로 해석되지 않고, 문자 그대로 사용된다. 

    Double Quote가 흔히 알고 있는 문자열인 Interpreted String Literal 이다. 


 
```go
rawLiteral := `메롱메롱\n`
interLiteral := `얼레리꼴레리\n`
```

    그리고 String은 immutable type이다. 한번 생성되면 수정할 수가 없다.

> Type Conversion

    Go에서는 암묵적인 type conversion이 이뤄지지 않는다. 항상 명시적으로 지정해 주어야 한다.

    Type(var)와 같이 표현한다.

 
```go
func main() {
    var i int = 100
    var u uint = uint(i)
    var f float32 = float32(i)  
    println(f, u)
 
    str := "ABC"
    bytes := []byte(str)
    str2 := string(bytes)
    println(bytes, str2)
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
