# FastAPI 환경 세팅 및 설치

## 1.FastAPI 환경 세팅 및 설치

#### 1.1. "fastapi" 폴더 생성 및 이동

```
D:\worksapce\pythonProject>mkdir fastapi
D:\worksapce\pythonProject>cd fastapi
```

#### 1.2. 프로젝트별 가상 환경 세팅

{% hint style="info" %}
가상환경 없이 패키지를 설치하게되면 패키지들끼리 충돌이 일어나는 경우가 있는데 불필요한 이슈를 피하기위해 가상환경을 사용하는것이다.

각 프로젝트마다 따로 가상환경을 세팅하기 때문에 패키지가 전역에 깔리지 않는다.
{% endhint %}

```
D:\worksapce\pythonProject\fastapi>python -m venv fastapienv 
```

#### 1.3. 가상환경 활성화

```
D:\worksapce\pythonProject\fastapi>fastapienv\Scripts\activate.bat
```

```
(fastapienv) D:\worksapce\pythonProject\fastapi>pip list
```

"pip list" 명령어로 확인해보면 다른 환경과 분리되어 패키지 설치됨을 알 수 있다.

#### 1.4. 가상환경에서 fastapi 패키지 설치

```
(fastapienv) D:\worksapce\pythonProject\fastapi>pip install fastapi
```

#### 1.5. 가상환경에서 웹서버 설치\[uvicorn]&#x20;

```
(fastapienv) D:\worksapce\pythonProject\fastapi>pip install "uvicorn[standard]"
```

#### 1.6. 테스트 코드 작성

D:\worksapce\pythonProject\fastapi 경로에 "books.py" 파일 생성 및 아래코드 작성

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/api-endpoint")
async def first_api():
    return {'message':'Hello Eric!'}
```

#### 1.7. 어플리케이션 실행

```
uvicorn books:app --reload
```

reload : 저장된 파일에 변화가 있을때마다 uvicorn 서버를 다시 로드시킴 **(개발 환경에서만 사용 권장)**

```
(fastapienv) D:\worksapce\pythonProject\fastapi>uvicorn books:app --reload
INFO:     Will watch for changes in these directories: ['D:\\worksapce\\pythonProject\\fastapi']
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Started reloader process [13072] using WatchFiles
INFO:     Started server process [8640]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     127.0.0.1:64451 - "GET /api-endpoint HTTP/1.1" 200 OK
INFO:     127.0.0.1:64451 - "GET /favicon.ico HTTP/1.1" 404 Not Found
```

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

#### 1.8. 가상환경 비활성화

```
(fastapienv) D:\worksapce\pythonProject\fastapi>deactivate
D:\worksapce\pythonProject\fastapi>
```
