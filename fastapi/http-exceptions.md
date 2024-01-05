# HTTP Exceptions

```python
from fastapi import FastAPI,Path,Query,HTTPException # HTTPException 추가
```

* HTTP 예외는 메서드 내에서 발생시켜야합니다.
* 메서드의 기능을 취소하고 상태 코드 메시지를 사용자에게 리턴한다

```python
@app.get("/books/{book_id}")
async def read_book(book_id: int = Path(gt=0)):
    for book in BOOKS:
        if book.id == book_id:
            return book
```

경로 매개 변수로 책 번호가 필요로 하는데 경로 매개변수로 한 책 번호에 해당하는 책이 없어도 404를 반환하지 않습니다. 이 API를 호출 했을때 서버에 없다는걸 사용자에게 알려줘야합니다



아래와 같이 수정

```python
@app.get("/books/{book_id}")
async def read_book(book_id: int = Path(gt=0)):
    for book in BOOKS:
        if book.id == book_id:
            return book
    raise HTTPException(status_code=404,detail='Item not found')
```

이제 존재 하지 않는 항목의 ID 값을 입력하면 (ex: 1888)

404 코드와 메시지를 리턴한다

* **response**

```json
{
    "detail":"Item not found"
}
```
