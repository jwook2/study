# 유효성검사

## 1. 유효성 검사가 필요한 이유

### 1.1.  POST API : Body Request 사용

* POST  API에서는 데이터를 전송할때 필요한Body 클래스를 Import 합니다.
* 보낼 데이터를 Body Request에 담아서 전송하도록 아래와 같이 작성합니다.

```python
from fastapi import Body,FastAPI

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

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

아래와 Body 를 작성하여 POST 전송시 별도의 오류메시지 없이 정상적으로 데이터가 등록이 됩니다

* 아래 Body 전문에서는 "id" 에 해당하는 값이 음수임에도 별도의 에러메시지 없이 정상적으로 등록이 되어 예상치 못한 문제가 발생 할 수 있어 이를 방지하기 위한 **예외처리 로직 또는 유효성 검사가 필요**합니다.

```json
// Request Body
{
    "id": -7,
    "title": "HP4",
    "author": "Author4",
    "description": "Book Descriotion",
    "rating": 3
}
```



**Response \[200]**

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

### 1.2. 해결방법

* 로직을 통한 예외처리
  * ID 값을 자동으로 세팅해주는 함수를 생성하여 예외처리

```python
def find_book_id(book: Book):
    if len(BOOKS)>0 :
        book.id = BOOKS[-1].id + 1
    else:
        book.id = 1
    
    return book
    
@app.post("/create-book")
async def create_book(book_request=Body()):
    BOOKS.append(find_book_id(book_request))
```

* Pydantic  라이브러리를 통한 예외처리

```python
from fastapi import FastAPI , Body
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
        
# 데이터 검증을 위한 요청객체 생성  
class BookRequest(BaseModel):
    id: int
    title: str
    author: str
    description: str
    rating: int

BOOKS = [
    Book(1,'Computer Science Pro','codingwithroby','A very nice book!',5),
    Book(2,'Be Fast with FastAPI','codingwithroby','A great book!',5),
    Book(3,'Master Endpoints','codingwithroby','A awesome book!',5),
    Book(4,'HP1','Author1','Book Descriotion',2),
    Book(5,'HP2','Author2','Book Descriotion',3),
    Book(6,'HP3','Author3','Book Descriotion',1)
]

# Body 대신 새로 생성한 요청객체를 타입으로 POST
# 검증을 통해 요청과 일치한지 확인하게 됨
@app.post("/create-book")
async def create_book(book_request:BookRequest):
    #new_book = Book(**book_request.model_dump())
    BOOKS.append(new_book)
```

What is Pydantics

* 효율적인 오류 처리 핸들링을 할 수 있게 해주는 라이브러리
* 데이터와 기본 모델에 대한 유효성 검사를 할 수 있게 해주는 프레임워크
* 객체가 들어오면 객체 내의 변수를 검사 할 수 있습니다.

```python
from fastapi import FastAPI , Body
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
        
# 데이터 검증을 위한 요청객체 생성  
class BookRequest(BaseModel):
    id: int
    title: str
    author: str
    description: str
    rating: int

BOOKS = [
    Book(1,'Computer Science Pro','codingwithroby','A very nice book!',5),
    Book(2,'Be Fast with FastAPI','codingwithroby','A great book!',5),
    Book(3,'Master Endpoints','codingwithroby','A awesome book!',5),
    Book(4,'HP1','Author1','Book Descriotion',2),
    Book(5,'HP2','Author2','Book Descriotion',3),
    Book(6,'HP3','Author3','Book Descriotion',1)
]

# Body 대신 새로 생성한 요청객체를 타입으로 요청함
# 검증을 통해 요청과 일치한지 확인하게 됨
@app.post("/create-book")
async def create_book(book_request:BookRequest):
    BOOKS.append(book_request)
    print(type(book_request)) #<class 'books2.BookRequest'>
```

아래와 같이 개선

```python
@app.post("/create-book")
async def create_book(book_request:BookRequest):
    new_book = Book(**book_request.model_dump())
    # 앞에 **가 의미하는건? > 
    print(type(new_book)) #<class 'books2.Book'>
    BOOKS.append(new_book)
```

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

맞지않는 요청 타입 데이터를 보냈을때 아래와 같이 에러메시지가 발생

```json
{
  "detail": [
    {
      "type": "int_parsing",
      "loc": [
        "body",
        "rating"
      ],
      "msg": "Input should be a valid integer, unable to parse string as an integer",
      "input": "삼십",
      "url": "https://errors.pydantic.dev/2.5/v/int_parsing"
    }
  ]
}
```

### 2. 각 필드에 Validation 적용하기

```python
from fastapi import FastAPI
from pydantic import BaseModel,Field # Field 클래스 추가

class BookRequest(BaseModel):
    id: int
    title: str = Field(min_length=3) # 최대길이=3
    author: str = Field(min_length=1) # 최소길이=1
    description: str = Field(min_length=1,max_length=100) #최소길이=1,최대길이=100
    rating: int = Field(gt=0,lt=6) # 1에서 5까지

@app.post("/create-book")
async def create_book(book_request:BookRequest):
    new_book = Book(**book_request.model_dump())
    BOOKS.append(new_book)
```

#### 2.1 필드에 Optional 적용하기

```python
from typing import Optional

class BookRequest(BaseModel):
    id: Optional[int] = None # 선택사항이길 원할때
    title: str = Field(min_length=3) # 최대길이=3
    author: str = Field(min_length=1) # 최소길이=1
    description: str = Field(min_length=1,max_length=100) #최소길이=1,최대길이=100
    rating: int = Field(gt=0,lt=6) # 1에서 5까지

```

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>
