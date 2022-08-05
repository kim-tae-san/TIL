# Context

    Golang에서 컨텍스트(Context)는 작업 명세서와 같은 역할로, 작업 가능한 시간, 작업 취소 등 작업의 흐름을 제어하는데 사용됩니다.

    Golang에서는 다음과 같이 context 패키지를 사용하여 컨텍스트를 정의할 수 있습니다.
```go
import "context"

// Cancel
ctx, cancel := context.WithCancel(context.Background())
// Deadline
ctx, cancel := context.WithDeadline(context.Background(), TIME)
// Timeout
ctx, cancel := context.WithTimeout(context.Background(), DURATION)
```

## WithCancel

    컨텍스트가 cancel 혹은 timeout으로 종료되면 컨텍스트의 Done이 호출됩니다. 여기서는 cancel을 사용하여 컨텍스트를 종료 시키는 방법에 대해서 알아보도록 하겠습니다.
```go
ctx, cancel := context.WithCancel(context.Background())
```
    이를 확인하기 위해 main.go 파일을 생성하고 다음과 같이 수정합니다.

```go
package main

import (
  "context"
  "fmt"
  "sync"
  "time"
)

var wg sync.WaitGroup

func main() {
  wg.Add(1)
  ctx, cancel := context.WithCancel(context.Background())

  go PrintTick(ctx)

  time.Sleep(5 * time.Second)
  cancel()

  wg.Wait()
}

func PrintTick(ctx context.Context) {
  tick := time.Tick(time.Second)
  for {
    select {
    case <-ctx.Done():
      fmt.Println("Done:", ctx.Err())
      wg.Done()
      return
    case <-tick:
      fmt.Println("tick")
    }
  }
}
```

    이를 실행하면 다음과 같은 결과를 얻을 수 있습니다.

```go
# go run main.go
tick
tick
tick
tick
tick
Done: context canceled
```

    소스코드를 자세히 살펴보면 WaitGroup을 사용하여 메인 고루틴이 서브 고루틴을 기다릴 수 있도록 하였습니다.
```go
var wg sync.WaitGroup

func main() {
  wg.Add(1)
  ...
  wg.Wait()
}
```
    
    그리고 context 패키지를 사용하여 컨텍스트를 생성하였고, 이를 서브 고루틴에게 전달하였습니다.
```go
func main() {
  ...
  ctx, cancel := context.WithCancel(context.Background())

  go PrintTick(ctx)
  ...
}
```

    서브 고루틴으로 실행된 PrintTick 함수는 time 패키지의 Tick 함수를 사용하여 매초 신호를 발생시키는 채널을 생성하였습니다. 또한 switch 문을 사용하여 컨텍스트의 Done 채널과 time 패키지의 Tick 함수로 생성한 채널에서 데이터를 기다리도록 했습니다.
```go
func PrintTick(ctx context.Context) {
  tick := time.Tick(time.Second)
  for {
    select {
    case <-ctx.Done():
      fmt.Println("Done:", ctx.Err())
      wg.Done()
      return
    case <-tick:
      fmt.Println("tick")
    }
  }
}
```
    Tick 함수로 생성한 채널에서 데이터가 들어오면, tick이라는 문자열을 화면에 출력하였으며, 컨텍스트의 Done 함수 채널에서 데이터가 들어오면 화면에 Done 문자와 종료 이유(ctx.Err())를 표시하게 됩니다. 그후 WaitGroup의 Done 함수를 호출하여, 서브 고루틴이 종료되었음을 메인 고루틴에게 알렸습니다.
```go
func main() {
  ...
  ctx, cancel := context.WithCancel(context.Background())

  go PrintTick(ctx)

  time.Sleep(5 * time.Second)
  cancel()
  ...
}
```
    메인 고루틴에서는 PrintTick 함수를 실행한 후, 5초후 컨텍스트의 cancel을 호출하여 컨텍스트를 종료시켰습니다. 따라서 화면에는 tick이 5번 출력되고, 컨텍스트의 종료 이유가 화면에 표시 된 후, 프로그램이 종료되는 것을 확인할 수 있습니다.

## WithDeadline

    컨텍스트의 Deadline은 작업 흐름(고루틴)을 언제까지 유지할지 결정할 때 사용합니다.

```go
ctx, cancel := context.WithDeadline(context.Background(), TIME)
```

    이를 확인하기 위해 main.go 파일을 다음과 같이 수정합니다.

```go
package main

import (
  "context"
  "fmt"
  "sync"
  "time"
)

var wg sync.WaitGroup

func main() {
  wg.Add(1)

  d := time.Now().Add(3 * time.Second)
  ctx, cancel := context.WithDeadline(context.Background(), d)

  go PrintTick(ctx)

  time.Sleep(time.Second * 5)
  cancel()

  wg.Wait()
}

func PrintTick(ctx context.Context) {
  tick := time.Tick(time.Second)
  for {
    select {
    case <-ctx.Done():
      fmt.Println("Done:", ctx.Err())
      wg.Done()
      return
    case <-tick:
      fmt.Println("tick")
    }
  }
}
```
    이를 실행하면 다음과 같은 결과를 얻을 수 있습니다.

```go
# go run main.go
tick
tick
tick
Done: context deadline exceeded
```
    cancel을 설명할 때 사용한 예제에서, 컨텍스트를 생성할 때, 지금으로부터 3초후 컨텍스트를 종료시키기 위해 데드라인을 지정하였습니다.
```go
d := time.Now().Add(3 * time.Second)
ctx, cancel := context.WithDeadline(context.Background(), d)
```
    따라서 tick이 세번 화면에 표시된 후, 데드라인에 의해 컨텍스트가 종료되는 것을 확인할 수 있습니다. 하지만, 메인 고루틴은 5초간 유지되므로, 5초후에 프로그램이 종료되는 것을 확인할 수 있습니다.
```go
func main() {
  ...
  d := time.Now().Add(3 * time.Second)
  ctx, cancel := context.WithDeadline(context.Background(), d)
  ...
  time.Sleep(time.Second * 5)
  cancel()
  ...
}
```
    데드라인을 지정하여 컨텍스트를 종료시키더라도, cancel 함수를 사용하여 컨텍스트를 닫아주는 코드를 꼭 추가해야 합니다.

## WithTimeout

    컨텍스트의 Timeout은 작업 흐름(컨텍스트)을 얼마간 유지할지 결정할 때 사용합니다.
```go
ctx, cancel := context.WithTimeout(context.Background(), TIME)
```
이를 확인하기 위해 main.go 파일을 다음과 같이 수정합니다.
```go
package main

import (
  "context"
  "fmt"
  "sync"
  "time"
)

var wg sync.WaitGroup

func main() {
  wg.Add(1)

  ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)

  go PrintTick(ctx)

  time.Sleep(time.Second * 5)
  cancel()

  wg.Wait()
}

func PrintTick(ctx context.Context) {
  tick := time.Tick(time.Second)
  for {
    select {
    case <-ctx.Done():
      fmt.Println("Done:", ctx.Err())
      wg.Done()
      return
    case <-tick:
      fmt.Println("tick")
    }
  }
}
```
    이를 실행하면 다음과 같은 결과를 얻을 수 있습니다.
```go
# go run main.go
tick
tick
tick
Done: context deadline exceeded
```
    결과는 Deadline을 사용하였을 때와 동일한 것을 알 수 있습니다. 둘의 차이점은 Deadline은 언제까지 유지할 것인지를 지정한다면, Timeout은 얼마간 유지할지를 지정합니다.
```go
// Deadline
d := time.Now().Add(3 * time.Second)
ctx, cancel := context.WithDeadline(context.Background(), d)
// Timeout
ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
```

    타임아웃 역시, 타임아웃에 의해 컨텍스트를 종료시키더라도, cancel 함수를 사용하여 컨텍스트를 닫아주는 코드를 꼭 추가해야 합니다.
 
## WithValue
    
    컨텍스트의 WithValue를 사용하여 채널과 같이 서브 고루틴에 데이터를 전달할 수 있습니다.
```go
ctx := context.WithValue(context.Background(), KEY, VALUE)

v := ctx.Value(KEY)
```

    이를 확인하기 위해 main.go 파일을 다음과 같이 수정합니다.

```go
package main

import (
  "context"
  "fmt"
  "sync"
)

var wg sync.WaitGroup

func main() {
  wg.Add(1)

  ctx := context.WithValue(context.Background(), "v", 3)

  go square(ctx)

  wg.Wait()
}

func square(ctx context.Context) {
  if v := ctx.Value("v"); v != nil {
    n := v.(int)
    fmt.Println("Square:", n*n)
  }
  wg.Done()
}
```
이를 실행하면 다음과 같은 결과를 얻을 수 있습니다.

```go
# go run main.go
Square: 9
```

## 컨텍스트 랩핑

    Golang에서 컨텍스트는 다음과 같이 랩핑(Wrapping)하여 사용할 수 있습니다.
```go
ctx, cancel := context.WithCancel(context.Background())
ctx = context.WithValue(ctx, "key", "value")
ctx = context.WithValue(ctx, "key2", "value2")
```
    이를 확인하기 위해 main.go 파일을 다음과 같이 수정합니다.
```go
package main

import (
  "context"
  "fmt"
  "sync"
  "time"
)

var wg sync.WaitGroup

func main() {
  wg.Add(1)
  ctx, cancel := context.WithCancel(context.Background())
  ctx = context.WithValue(ctx, "s", 2)

  go PrintTick(ctx)

  time.Sleep(5 * time.Second)
  cancel()

  wg.Wait()
}

func PrintTick(ctx context.Context) {
  tick := time.Tick(time.Second)

  if v := ctx.Value("s"); v != nil {
    s := v.(int)
    tick = time.Tick(time.Duration(s) * time.Second)
  }

  for {
    select {
    case <-ctx.Done():
      fmt.Println("Done:", ctx.Err())
      wg.Done()
      return
    case <-tick:
      fmt.Println("tick")
    }
  }
}
```
    이를 실행하면 다음과 같은 결과를 얻을 수 있습니다.
```go
# go run main.go
tick
tick
Done: context canceled
```
    WithCancel 예제에서 WithValue를 사용하여 컨텍스트를 랩핑하였습니다.
```go
func main() {
  ...
  ctx, cancel := context.WithCancel(context.Background())
  ctx = context.WithValue(ctx, "s", 2)
  ...
}
```
    이렇게 컨텍스트를 랩핑하여 컨텍스트를 통해 값을 전달하였으며, 전달받은 값을 사용하여 tick 문자열을 1초가 아닌 2초에 한번 표시되도록 하였습니다.
```go
func PrintTick(ctx context.Context) {
  tick := time.Tick(time.Second)

  if v := ctx.Value("s"); v != nil {
    s := v.(int)
    tick = time.Tick(time.Duration(s) * time.Second)
  }
  ...
}
```
    이와 같이 컨텍스트는 여러번 랩핑하여 필요한 작업 명세서를 작성할 수 있습니다.

출처 : https://dev-yakuza.posstree.com/ko/golang/context/