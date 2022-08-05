# DialContext
```go
func DialContext(ctx context.Context, target string, opts ...DialOption) (conn *ClientConn, err error)
```

    여기서 Default로 non-blocking dial을 제공하는데 이는 unary로 보면된다. blocking dial 통신을 원할 경우 option에 WithBlock()항목을 추가해주면 된다.   

# sync.Map

    Golang 동시성은 공유 리소스에 액세스 하는 경우 충돌이 발생하지 않도록 잠금을 준비해야 한다.
    예를 들어 표준 맵의 경우 goroutine safe 하지 않기 때문에, Write끼리 충돌하거나 Write중에 Read가 발생한 경우 Panic을 일으킨다.
    따라서 기존 맵과 sync.RWMutex를 함께 사용하여 잠금을 준비하고 여러 goroutine에서 안전하게 액세스 할 수 있도록 하는 것이 정석이었다. 

    Go1.9에서 sync.Map이 표준 패키지에 들어와서 이제 sync.RWMutex를 쓰지 않아도 잠금 장치가 있는 맵을 사용할 수 있게 되었다. 