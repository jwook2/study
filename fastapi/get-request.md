# GET Request

## 1. 데이터 세팅

<pre class="language-python"><code class="lang-python"><strong>BOOKS = {
</strong>    {'title':'Title One','author':'Author One','category':'science'},
    {'title':'Title Two','author':'Author Two','category':'science'},
    {'title':'Title Three','author':'Author Three','category':'history'},
    {'title':'Title Four','author':'Author Four','category':'math'},
    {'title':'Title Five','author':'Author Five','category':'math'},
}
</code></pre>

## 2.GET Request

### 2.1. 전체 조회

request : 127.0.0.1:8000/books

```python
@app.get("/books")
async def read_all_books():
    return BOOKS
```

response

```json
[
  {
    "title": "Title One",
    "author": "Author One",
    "category": "science"
  },
  {
    "title": "Title Two",
    "author": "Author Two",
    "category": "science"
  },
  {
    "title": "Title Three",
    "author": "Author Three",
    "category": "history"
  },
  {
    "title": "Title Four",
    "author": "Author Four",
    "category": "math"
  },
  {
    "title": "Title Five",
    "author": "Author Five",
    "category": "math"
  },
  {
    "title": "Title Six",
    "author": "Author Two",
    "category": "math"
  }
]
```

### 2.2. Path Parameters

request : 127.0.0.1:8000/books/Title Two

```python
@app.get("/books/{book_title}")
async def read_all_books(book_title:str):
    for book in BOOKS:
        if book.get('title').casefold() == book_title.casefold();
            return book
```

response

```json
{
  "title": "Title Two",
  "author": "Author Two",
  "category": "science"
}
```



### 2.3. Query Parameters

request : 127.0.0.1:8000/books/?category=science

<pre class="language-python"><code class="lang-python"><strong>@app.get("/books/")
</strong>async def read_category_by_query(category: str):
    books_to_return = []
    for book in BOOKS:
        if book.get('category').casefold() == category.casefold():
            books_to_return.append(book)
    
    return books_to_return
</code></pre>

response

```json
[
  {
    "title": "Title One",
    "author": "Author One",
    "category": "science"
  },
  {
    "title": "Title Two",
    "author": "Author Two",
    "category": "science"
  }
]
```



request : 127.0.0.1:8000/books/Author Two/?category=science

```python
@app.get("/books/{book_author}/")
async def read_category_by_query(book_author: str,category: str):
    books_to_return = []
    for book in BOOKS:
        if book.get('author').casefold() == book_author.casefold() and book.get('category').casefold() == category.casefold():
            books_to_return.append(book)
            
    return books_to_return
```

response

```json
[
  {
    "title": "Title Two",
    "author": "Author Two",
    "category": "science"
  }
]
```

{% hint style="info" %}
FastAPI 에서는 별도의 스웨거 파일 정의 없이 자동으로 생성&#x20;

" 127.0.0.1:8000/docs " 경로에서 작업한 API 스웨거문서를 확인 가능
{% endhint %}
