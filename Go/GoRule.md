# Data Type

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

# 조건문

> if/else

    1. if 다음에는 반드시 Boolean식으로 표현한다. (0, 1 이런거 못 씀)

    2. () 안 쓰지만 {}는 무조건 써 줌.

    3. else if 혹은 else 를 쓸 때는 반드시 전 조건의 open braces( { )와 같은 라인에 써준다.

 
```go
if k == 1 {
    println("One")
} else if k == 2 {  //같은 라인
    println("Two")
} else {   //같은 라인
    println("Other")
}
```

    재밌는 게 하나 추가 된다. if문에서 for문 처럼 Optional Statement를 사용할 수 있다.

    Conditional Statement와는 세미콜론으로 구분해준다.

 
```go
if val := i * 2; val < max {
    println(val)
}
```

> Switch / Case

    C++의 switch 와는 좀 다른 점이 있다.

    C++ 의 switch는 case블럭 안에 break를 써주어서  switch문을 빠져나와야 하고, break를 써주지 않는다면 다음 case 블럭에 들어간다. Go에서는 그렇지 않고, 하나의 case 에 만족한다면 그 블럭을 실행하고 바로 switch문을 빠져 나온다. 

    만약 C++  의  switch처럼 다음 case 블럭들에도 들어가게 하고 싶다면 fallthrough 를 써주면 된다.

 
```go
func check(val int) {
    switch val {
    case 1:
        fmt.Println("1 이하")
        fallthrough
    case 2:
        fmt.Println("2 이하")
        fallthrough
    case 3:
        fmt.Println("3 이하")
        fallthrough
    default:
        fmt.Println("default 도달")
    }
}
```

    근데 C++ 에서 보다는 switch문이 좀 유용해 보인다. 

    먼저, case 뒤에  expression 없애서 if/else if/ else 문장을 단순화 시킬 수도 있다.

 
```go
func grade(score int) {
    switch {
    case score >= 90:
        println("A")
    case score >= 80:
        println("B")
    case score >= 70:
        println("C")
    case score >= 60:
        println("D")
    default:
        println("No Hope")
    }
}   
```

    심지어 변수의  type 을 검사할 수도 있다!

 
```go
switch v.(type) {
case int:
    println("int")
case bool:
    println("bool")
case string:
    println("string")
default:
    println("unknown")
} 
```

# Loop

> for

    while은 없고 for 만 있다.

    역시  () parentheses 는 사용하지 않는다. 다음과 같이 사용한다.

 
```go
for i := 1; i <= 100; i++ {
}

for n < 100 { // while문 역할 
}

for { // 무한반복
}
 ```

    python처럼 for range 문이 사용가능하다.

    근데 index 변수까지 변수가 2개 필요.

 
```go
names := []string{"은비야", "나도", "공부 시작했어"}
 
for index, name := range names {
    println(index, name)
}
 ```

    break <Label> 이 있다.

    반복문을 탈출해서 <Label>로 간다음, for문을 건너 뛰고 그 다음 문장을 실행한다.

 
```go
package main
 
func main() {
    i := 0
 
L1:
    for {
     
        if i == 0 {
            break L1
        }
    }
 
    println("OK")
}
```

# Collection

> Array

    언제나 그렇듯 zero base

    선언은 var <변수 이름> [배열크기]<type> 

    여기서 배열크기 또한 type을 구성하는 요소이다. [3]int와 [5]int는 다른 타입의 변수이다.

 
```go
var a [3]int
var a1 = [3]int{1, 2, 3}
var a3 = [...]int{1, 2, 3} //배열크기 자동으로

var a = [2][3]int{
        {1, 2, 3},
        {4, 5, 6},  //끝에 콤마 추가
}
```

> Slice

    전체적으로 벡터와 비슷한 개념으로 이해했다.

    선언은 배열과 똑같이 하는데, 대신 [] 안에 크기는 지정해주지 않는다.
```go
func main() {
    var s []int
    var a []int{1, 2, 3}
 
    if s == nil {
        println("Nil Slice")
    }
    println(len(s), cap(s)) // 모두 0
}
 ```

    make라는 함수를 이용해 slice를 만들어 줄 수도 있다.

 
```go
a := make([]int, 5, 10) // slice with len :5, capacity : 10
 ```

    파이썬처럼 index를 활용해 sub slice를 만들어 줄 수 있다.

    언제나 그렇듯 slice[start idx : end idx +1 ] 되어 주시겠다.

 
```go
s := []int{0, 1, 2, 3, 4, 5}
s = s[2:5]     // 2, 3, 4
s = s[1:]      // 3, 4
 ```

    slice와 slice는 append()를 사용해 연결할 수 있다.

    주의할 점은 붙일 slice 중 두번째에 ellipsis(...)를 붙여야 한다는 것.

    Go에서의 ellipsis는 여러 의미로 사용되는데, 여기서는 "unpacking" 의 의미이다. 해당 slice의 컬렉션을 표현하는 것( slice의 모든 element들의 집합)이래서 뭔가 했는데, 대충 slice에 있는 element들을 unpack해서 append같은 variadic function에 넣어준다는 것 같음.

```go
package main
 
import "fmt"
 
func main() {
    sliceA := []int{1, 2, 3}
    sliceB := []int{4, 5, 6}
 
    sliceA = append(sliceA, sliceB...)
    //sliceA = append(sliceA, 4, 5, 6)
 
    fmt.Println(sliceA) // [1 2 3 4 5 6] 출력
}
 

copy()를 활용하여 복사할 수도 있다.

 

source := []int{0, 1, 2}
target := make([]int, len(source), cap(source)*2)
copy(target, source)
```

> Map

    드디어 Hashtable! 다음과 같이 두 가지 방법으로 선언할 수 있다.
```go
map[key type]value type

 

var idMap map[int]string
idMap = make(map[int]string)
 ```

    후에 Go interface에서 다룰건데, 이걸 활용하면 python의 dict처럼 사용할 수 있다.

 

    Map에서 특정 key가 존재하는지를 체크하는 방법은 다음과 같다.

    map변수 [key] 읽기를 수행할 때 2개의 리턴값이 있는데, 이 때 두번째 값이 그 키가 존재하는지 나타내는 bool값이다. 이 것을 체크해주면 된다.

 
```go
package main
 
func main() {
    tickers := map[string]string{
        "GOOG": "Google Inc",
        "MSFT": "Microsoft",
        "FB":   "FaceBook",
        "AMZN": "Amazon",
    }
 
    // map 키 체크
    val, exists := tickers["MSFT"]
    if !exists {
        println("No MSFT ticker")
    }
}
 ```

    사실 value값의 범위에 0만 포함되지 않으면 key에 해당하는 value가 0인지 아닌지만 체크하면 된다. 만약 value가 reference라면 nil인지만 체크하면 된다. 하지만 이건 범용적인 범위가 아니므로 패스

    +Map은 unordered인 hash이므로 순서가 무작위이다. 따라서 for range를 이용했을 때 모든 매 요소가 매번 다른 순서로 반복문에 돌려진다.


출처 : https://gyurious.tistory.com/42?category=915675