# Fast API 폴더 구조 잡기

{% embed url="https://fastapi.tiangolo.com/tutorial/bigger-applications/#folders" %}

## 파이썬 공식문서에 나와있는 폴더 구조

```
.
├── app                  # "app" is a Python package
│   ├── __init__.py      # this file makes "app" a "Python package"
│   ├── main.py          # "main" module, e.g. import app.main
│   ├── dependencies.py  # "dependencies" module, e.g. import app.dependencies
│   └── routers          # "routers" is a "Python subpackage"
│   │   ├── __init__.py  # makes "routers" a "Python subpackage"
│   │   ├── items.py     # "items" submodule, e.g. import app.routers.items
│   │   └── users.py     # "users" submodule, e.g. import app.routers.users
│   └── internal         # "internal" is a "Python subpackage"
│       ├── __init__.py  # makes "internal" a "Python subpackage"
│       └── admin.py     # "admin" submodule, e.g. import app.internal.admin
```



```python
## file_name : main.py
from fastapi import FastAPI

# routers 패키지에서 users 모듈. 
from routers import users 

################################################################################################

app = FastAPI()
app.include_router(users.router)

@app.get("/")
async def root():
    return {"message": "Hello World"}
```

```python
## file_name : users.py
from fastapi import APIRouter

router = APIRouter()

@router.get("/users/", tags=["users"])
async def read_users():
    return [{"username": "Rick"}, {"username": "Morty"}]

@router.get("/users/{username}", tags=["users"])
async def read_user(username: str):
    return {"username": username}

@router.get("/lee/", tags=["lee"])
async def read_users():
    return {"username": "lee"}
```

## 폴더 구조 설계

```
.
├── app                  # 
│   ├── __init__.py      #
│   ├── main.py          # 
│   ├── dependencies.py  #
│   ├── logs             # 로그를 저장하고 관리
│   └── database         # 데이터베이스 연결/설정을 관리
│   │   ├── __init__.py  #
│   │   └── database.py  #
│   └── routers          # 
│   │   ├── __init__.py  # 
│   │   ├── items.py     # 
│   │   └── users.py     # 
│   └── common           #
│   │   ├── __init__.py  #
│   │   └── config.py    #
│   └── schemas          # 
│   │   ├── __init__.py  # 
│   │   └── itemBase.py  # 
│   └── models
│       ├── __init__.py  # 
│       └── item.py      #        
```

모델(Model)은 데이터를 표현하는 클래스로, 비즈니스 로직을 구현하는 데 사용됩니다.&#x20;

스키마는 데이터의 형식을 정의하고 검증하는 데 중점을 두지만, 모델은 데이터를 처리하고 조작하는 데 사용됩니다.

[https://tech.osci.kr/fastapi-%ED%8C%8C%EC%9D%B4%EC%8D%AC%EC%9C%BC%EB%A1%9C-%EA%B0%84%EB%8B%A8%ED%95%98%EA%B2%8C-%EC%9B%B9-api-%EB%A7%8C%EB%93%A4%EA%B8%B0/](https://tech.osci.kr/fastapi-%ED%8C%8C%EC%9D%B4%EC%8D%AC%EC%9C%BC%EB%A1%9C-%EA%B0%84%EB%8B%A8%ED%95%98%EA%B2%8C-%EC%9B%B9-api-%EB%A7%8C%EB%93%A4%EA%B8%B0/)
