# FastApi 정리

> fixed path, get data path 

    코드가 작성된 순서대로 판단된다. 

```py
@app.get("/users/me")
async def read_user_me():
    return {"user_id": "the current user"}


@app.get("/users/{user_id}")
async def read_user(user_id: str):
    return {"user_id": user_id}
```

    위의 코드대로라면 첫번째로 선언된 모듈의 영향을 받아 the current user창을 보게된다.


```py
@app.get("/users")
async def read_users():
    return ["Rick", "Morty"]


@app.get("/users")
async def read_users2():
    return ["Bean", "Elfo"]
```

    위의 코드대로라면 역시 첫번째로 선언된 모듈의 영향을 받아 Rick, Morty가 출력된다.


> Predefined values

    Enum을 상속하여 Predefined Values class를 만들 수 있다.

```py
from enum import Enum

from fastapi import FastAPI


class ModelName(str, Enum):
    alexnet = "alexnet"
    resnet = "resnet"
    lenet = "lenet"
```

    사용방법은 아래와 같다.

```py
@app.get("/models/{model_name}")
async def get_model(model_name: ModelName):
    if model_name == ModelName.alexnet:
        return {"model_name": model_name, "message": "Deep Learning FTW!"}

    if model_name.value == "lenet":
        return {"model_name": model_name, "message": "LeCNN all the images"}

    return {"model_name": model_name, "message": "Have some residuals"}
```

    여기서 model_name: ModelName 부분의 : ModelName은 타입 어노테이션이다. 


> Optional Parameters

```py
from typing import Union

from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
async def read_item(item_id: str, q: Union[str, None] = None):
    if q:
        return {"item_id": item_id, "q": q}
    return {"item_id": item_id}
```

    위 경우는 함수의 매개변수 q는 옵셔널하게 되겠고, 기본적으로 None형태를 띈다.

> Query parameter type conversion

```py
from typing import Union

from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
async def read_item(item_id: str, q: Union[str, None] = None, short: bool = False):
    item = {"item_id": item_id}
    if q:
        item.update({"q": q})
    if not short:
        item.update(
            {"description": "This is an amazing item that has a long description"}
        )
    return item
```


> Request Body

    Request Body를 선언하기 위해서 Pydantic Model을 활용하는게 좋다.

```py
from typing import Union

from fastapi import FastAPI
from pydantic import BaseModel


class Item(BaseModel):
    name: str
    description: Union[str, None] = None
    price: float
    tax: Union[float, None] = None


app = FastAPI()


@app.post("/items/")
async def create_item(item: Item):
    return item
```


> Request Body

    클라이언트로 부터 Request가 오면 Body를 Json Format으로 읽어 정보를 가져온다. 중간에 데이터를 가공해서 보낼수도 있다. 