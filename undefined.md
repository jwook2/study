---
description: 경로 매개변수에 대한 유효성 검사를 진행합니다
---

# 경로 매개 변수와 쿼리 검증

```python
import fastapi import FastAPI,Path,Query

@app.get("/books/{book_id}")
async def read_book(book_id:int = Path(gt=0)): #보다 커야함
    for book in BOOKS:
        if book.id == book_id:
            return book
```
