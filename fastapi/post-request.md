# POST Request

{% hint style="info" %}
### POST Request

* 클라이언트로 데이터를 보내야 하는경우 request body를 보내야 합니다.
* request body 는 클라이언트가 API로 보낸 데이터입니다.
* 데이터를 보낼때는 POST 를 사용합니다.
{% endhint %}

## Request Body 값 전송하기

```python
from fastapi import Body,FastAPI # Body 라이브러리 추가

app = FastAPI()

class Book:
    id: int
    title: str
    author: str
    description: str 
    rating: int
        
    def __init__(self,id,title,author,description,rating):
        self.id = id
        self.title = title
        self.author = author
        self.description = description
        self.rating = rating

BOOKS = [
    Book(1,'Computer Science Pro','codingwithroby','A very nice book!',5),
    Book(2,'Be Fast with FastAPI','codingwithroby','A great book!',5),
    Book(3,'Master Endpoints','codingwithroby','A awesome book!',5),
    Book(4,'HP1','Author1','Book Descriotion',2),
    Book(5,'HP2','Author2','Book Descriotion',3),
    Book(6,'HP3','Author3','Book Descriotion',1)
]

@app.post("/create-book")
async def create_book(book_request=Body()):
    BOOKS.append(book_request)
```

FastAPI에서 제공하는 Body 클래스를 사용하여 Body 데이터를 처리합니다.

하지만, 이 Body 클래스를 사용하여 데이터를 전송시 타입검사가 어려운 단점과 불편함이 있어&#x20;

Fast API 공식문서에서는 Request body를 선언할 때 [Pydantic](https://pydantic-docs.helpmanual.io/) 모델을 사용하는 것을 권장합니다.

* **아래와 같이 전송 Body 데이터에 대한 검사가 없어 잘못된 값이 등록되는 경우가 생길 수 있다.**

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

## BaseModel

* Body 클래스 대신 pydantic 라이브러리에서 제공하는 BaseModel을 사용
* **BaseModel에 정의한것과 다르게 요청시 에러가 발생됨**

```python
from fastapi import FastAPI
from pydantic import BaseModel # 추가

app = FastAPI()

class Book:
    id: int
    title: str
    author: str
    description: str
    rating: int
    
    def __init__(self,id,title,author,description,rating):
        self.id = id
        self.title = title
        self.author = author
        self.description = description
        self.rating = rating
        
# 데이터 검증을 위한 BaseModel 정의
class BookRequest(BaseModel):
    id: int
    title: str
    author: str
    description: str
    rating: int


BOOKS = [
    {'title': 'Title One', 'author': 'Author One', 'category': 'science'},
    {'title': 'Title Two', 'author': 'Author Two', 'category': 'science'},
    {'title': 'Title Three', 'author': 'Author Three', 'category': 'history'},
    {'title': 'Title Four', 'author': 'Author Four', 'category': 'math'},
    {'title': 'Title Five', 'author': 'Author Five', 'category': 'math'},
    {'title': 'Title Six', 'author': 'Author Two', 'category': 'math'}
]

@app.post("/books/create_book")
async def create_book(book_request:BookRequest): 
    BOOKS.append(book_request)
```

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

## Field

* pydantic 라이브러리에서 제공하는 Field 를 사용하여 각 필드에 대한 유효성 처리를 할 수 있다.

```python
from fastapi import FastAPI
from pydantic import BaseModel,Field # Field 추가 

app = FastAPI()

class Book:
    id: int
    title: str
    author: str
    description: str
    rating: int
    
    def __init__(self,id,title,author,description,rating):
        self.id = id
        self.title = title
        self.author = author
        self.description = description
        self.rating = rating
        
# Field 유효성 처리 추가
class BookRequest(BaseModel):
    id: int
    title: str = Field(min_length=3) #최소길이3
    author: str = Field(min_length=1) #최소길이1
    description: str = Field(min_length=1,max_length=100) #최소길이1,최대길이 100
    rating: int = Field(gt=-1,lt=6) #-1보다 크고 6보다 작다

BOOKS = [
    {'title': 'Title One', 'author': 'Author One', 'category': 'science'},
    {'title': 'Title Two', 'author': 'Author Two', 'category': 'science'},
    {'title': 'Title Three', 'author': 'Author Three', 'category': 'history'},
    {'title': 'Title Four', 'author': 'Author Four', 'category': 'math'},
    {'title': 'Title Five', 'author': 'Author Five', 'category': 'math'},
    {'title': 'Title Six', 'author': 'Author Two', 'category': 'math'}
]

@app.post("/books/create_book")
async def create_book(book_request:BookRequest): 
    BOOKS.append(book_request)
```

## 추가 예외처리

* id 값을 자동으로 세팅하도록 편의 함수 생성

```python
# 등록된 도서 목록이 0개가 아니라면 등록되어 있는 도서목록  마지막 ID값+1 한 값을 가져옴
def find_book_id(book: Book):
    book.id = 1 if len(BOOKS) == 0 else BOOKS[-1].id + 1
    return book
    
# 예외처리 함수 적용
@app.post("/create-book")
async def create_book(book_request: BookRequest):
    # new_book = Book(**book_request.dict())
    new_book = Book(**book_request.model_dump()) 
    BOOKS.append(find_book_id(new_book));

```

"id" 값을 자동으로 세팅 할 수 있게 되어 등록 시 Body 데이터에는 "ID" 값을 세팅해서 보낼 필요가 없어졌으므로 아래와 같이 처리

* typing 모듈의 Optional은 None이 허용되는 매개변수에 대한 타입을 명시할 때 유용

```python
from typing import Optional #추가
```

* BaseModel 수정

```python
class BookRequest(BaseModel):
    id: Optional[int] = None # 필수값이 아니게됨
    title: str = Field(min_length=3)
    author: str = Field(min_length=1)
    description: str = Field(min_length=1,max_length=100)
    rating: int = Field(gt=0,lt=6) # 0보다 크고 6보다 작아야 함
```

## 요청 예시 데이터 선언

Request Body에 담을 데이터의 예시를 선언 할 수 있습니다.

선언한 BaseModel 하위에 요청 예시 데이터 선언

```python
class BookRequest(BaseModel):
    id: Optional[int] = None # 필수값이 아니게됨
    title: str = Field(min_length=3)
    author: str = Field(min_length=1)
    description: str = Field(min_length=1,max_length=100)
    rating: int = Field(gt=0,lt=6) # 0보다 크고 6보다 작아야 함
    
    class Config:
        json_schema_extra = { #Pydantic1 버전 사용시 schema_extra={}
            'example':{
                'title':'A new book',
                'author':'codingwithroby',
                'description':'A new description of a book',
                'rating':5
            }
        } 
```

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>
