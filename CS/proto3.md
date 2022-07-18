# Proto3 문법 정리

> Message Type

```protobuf
syntax = "proto3";

message SearchRequest {
  string query = 1;
  int32 page_number = 2;
  int32 result_per_page = 3;
}
```

    이 코드를 보면 message definition 내부의 각 필드는 unique number를 가지고 있다. 이러한 유니크 넘버는 message binary format에서 각 필드를 식별하는데 사용된다. 그렇기 때문에 한번 사용되면 그 이후로는 바뀌면 안된다.

    필드 넘버 1~15는 1byte로 인코딩된다. 이때 필드넘버와 필드타입이 포함된다. 자세한건 ProtocolBufferEncoding으로.

    필드 넘버 16~2047은 2bytes를 차지한다. 그래서 1~15의 숫자는 매우 자주사용되는 message element에 할당하는것이 좋다.

    필드 넘버의 범위는 1부터 536,870,911 까지이다. 19000~19999까지는 Protocol Buffers Implementation을 위해 예약된 숫자이므로 사용될 수 없다. 


# Field Rules

  * Singular : 잘 짜여진 message는 이 필드를 최대 1개 가지 가질 수 있다. 이것을 추가하는 것은 proto3 문법의 기본 필드 룰이다.
  * repeated : 이 필드는 잘 짜여진 message에서 몇번이고 반복될 수 있다. 반복된 값의 준서는 보존된다. 

    proto3에서 scalar numeric 타입의 repeated필드는 기본적으로 packed 인코딩을 사용한다.

    