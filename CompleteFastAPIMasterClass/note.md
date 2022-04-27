# Complete FastAPI masterclass + Instagram clone project

## Section1. Introduction




## Section2. Getting started




## Section3. Get method




## Section4. Operation description


### Lecture14. Section overview
- FastAPIは、自動的にSwaggerが作成される。
- 本セクションは以下の記述について学習する。
    - ステータスコード
    - タグ
    - サマリと記述
    - レスポンスの記述

### Lecture15. Status code
- ステータスコードはレスポンスの状態を簡易的に返すモノである。
```python
from typing import Optional
from fastapi import FastAPI, status
from enum import Enum

app = FastAPI()

@app.get('/hello')
def index():
    return {'message': 'Hello, world!'}

@app.get('blog/{id}/comments/{comment_id}')
def get_comment(id: int, comment_id: int, valid: bool=True, username: Optional[str]=None):
    return {'message': f'blog_id {id}, comment_id {comment_id}, valid {valid}, username {username}'}

class BlogType(str, Enum):
    short = 'short'
    story = 'story'
    howto = 'howto'

@app.get('/blog/type/{type}')
def get_blog_type(type: BlogType):
    return {'message': f'Blog type {type}'}

@app.get('/blog/{id}')
def get_blog(id: int, response: Response):
    if id > 5:
        response.status_code = status.HTTP_404_NOT_FOUND
        return {'error': f'Blog {id} not found'}
    response.status_code = status.HTTP_200_OK
    return {'message': f'Blog with id {id}'}

```

### Lecture16. Tags
- タグは、APIをタグごとにまとめるために使用される。
```python
from typing import Optional
from fastapi import FastAPI, status
from enum import Enum

app = FastAPI()

@app.get('/hello')
def index():
    return {'message': 'Hello, world!'}

@app.get(
    '/blog/all',
    tag=['blog']
)
def get_all_blogs(page=1, page_size: Optional[int]=None):
    return {'message': f'All {page_size} blogs on page {page}'}

@app.get('blog/{id}/comments/{comment_id}')
def get_comment(id: int, comment_id: int, valid: bool=True, username: Optional[str]=None):
    return {'message': f'blog_id {id}, comment_id {comment_id}, valid {valid}, username {username}'}

class BlogType(str, Enum):
    short = 'short'
    story = 'story'
    howto = 'howto'

@app.get('/blog/type/{type}')
def get_blog_type(type: BlogType):
    return {'message': f'Blog type {type}'}

@app.get('/blog/{id}')
def get_blog(id: int, response: Response):
    if id > 5:
        response.status_code = status.HTTP_404_NOT_FOUND
        return {'error': f'Blog {id} not found'}
    response.status_code = status.HTTP_200_OK
    return {'message': f'Blog with id {id}'}

```

### Lecture17. Summary and Description
- サマリとディスクリプションを記述するために、以下の記述方法を使用する。
```python
from typing import Optional
from fastapi import FastAPI, status
from enum import Enum

app = FastAPI()

@app.get('/hello')
def index():
    return {'message': 'Hello, world!'}

@app.get(
    '/blog/all',
    summary="Retrive all blogs" # Summaryの第一の方法
    description="This api call simulates fetching all blogs." # descritionの第一の方法
    tag=['blog']
)
def get_all_blogs(page=1, page_size: Optional[int]=None):
    return {'message': f'All {page_size} blogs on page {page}'}

@app.get('blog/{id}/comments/{comment_id}')
def get_comment(id: int, comment_id: int, valid: bool=True, username: Optional[str]=None):
    """
    simulates retrieving a comment of a blog

    - **id** mandatory path parameter
    - **comment_id** mandatory path parameter
    - **valid** optinal query parameter
    - 
    """
    return {'message': f'blog_id {id}, comment_id {comment_id}, valid {valid}, username {username}'}

class BlogType(str, Enum):
    short = 'short'
    story = 'story'
    howto = 'howto'

@app.get('/blog/type/{type}')
def get_blog_type(type: BlogType):
    return {'message': f'Blog type {type}'}

@app.get('/blog/{id}')
def get_blog(id: int, response: Response):
    if id > 5:
        response.status_code = status.HTTP_404_NOT_FOUND
        return {'error': f'Blog {id} not found'}
    response.status_code = status.HTTP_200_OK
    return {'message': f'Blog with id {id}'}
```

### Lecture18. Response Description
- responseの記述は以下の方法に従う。
```python
@app.get(
    '/blog/all',
    summary="Retrive all blogs" 
    description="This api call simulates fetching all blogs."
    tag=['blog'],
    response_description="this is a list of response desctiption"   
)
def get_all_blogs(page=1, page_size: Optional[int]=None):
    return {'message': f'All {page_size} blogs on page {page}'}
```


## Section5. Routers


### Lecture19. Section overview
- 本セクションではルーターについて調査する。
    - Routerとは何か。
    - Appのリファクタ
    - 第二のRouterの追加

### Lecture20. Routers
- Routerは論理的な単位でAPIを分割するために使用される。
```python
from fastapi import APIRouter

blog_router = APIRouter(prefix='/blog', tags=['blog'])

@blog_router.get("/")
def 
```


### Lecture21. Refactoring the app
```python
from typing import Optional
from fastapi import FastAPI, status
from enum import Enum

app = FastAPI()

blog_router = APIRouter(prefix="/blog", tags=["blog"])
hello_router = APIRouter(prefix="/hello", tags=["hello"])

app.include_router(blog_router)
app.include_router(hello_router)

@hello_router.get('/hello')
def index():
    return {'message': 'Hello, world!'}

@blog_router.get(
    '/all',
    summary="Retrive all blogs" # Summaryの第一の方法
    description="This api call simulates fetching all blogs." # descritionの第一の方法
)
def get_all_blogs(page=1, page_size: Optional[int]=None):
    return {'message': f'All {page_size} blogs on page {page}'}

@blog_router.get('{id}/comments/{comment_id}')
def get_comment(id: int, comment_id: int, valid: bool=True, username: Optional[str]=None):
    """
    simulates retrieving a comment of a blog

    - **id** mandatory path parameter
    - **comment_id** mandatory path parameter
    - **valid** optinal query parameter
    - 
    """
    return {'message': f'blog_id {id}, comment_id {comment_id}, valid {valid}, username {username}'}

class BlogType(str, Enum):
    short = 'short'
    story = 'story'
    howto = 'howto'

@blog_router.get('type/{type}')
def get_blog_type(type: BlogType):
    return {'message': f'Blog type {type}'}

@blog_router.get('{id}')
def get_blog(id: int, response: Response):
    if id > 5:
        response.status_code = status.HTTP_404_NOT_FOUND
        return {'error': f'Blog {id} not found'}
    response.status_code = status.HTTP_200_OK
    return {'message': f'Blog with id {id}'}
```


### Lecture22. Adding a second router


## Section6. Parameters

### Lecture23. Section overview
```python
from fastapi import APIRouter

router = APIRouter(
    prefix='/blog',
    tags=['blog'],
)

@router.post('/new')
def create_blog():
    pass
```

### Lecture24. Request body
- fastapiではjsonからの変換にPydanticを使用する。また、この際に型変換やバリデーションが実施される。
```python
from fastapi import APIRouter
from pydantic import BaseModel

router = APIRouter(
    prefix='/blog',
    tags=['blog'],
)

class BlogModel(BaseModel):
    title: str
    content: str
    published: Optional[bool]

@router.post('/new')
def create_blog(blog: BlogModel):
    return {"data": blog}
```

### Lecture25. Path and Query parameters
```python
from fastapi import APIRouter
from pydantic import BaseModel

router = APIRouter(
    prefix='/blog',
    tags=['blog'],
)

class BlogModel(BaseModel):
    title: str
    content: str
    published: Optional[bool]

# この場合、最初に来るのがPostのRequest Parameter、次にPath Parameter、最後にQuery Parameterとなる。
@router.post('/new/{id}')
def create_blog(blog: BlogModel, id: int, version: int = 1):
    return {
        "data": blog,
        "id": id,
        "version": version
    }
```

### Lecture26. Parameter metadata
```python
from fastapi import APIRouter
from pydantic import BaseModel

router = APIRouter(
    prefix='/blog',
    tags=['blog'],
)

class BlogModel(BaseModel):
    title: str
    content: str
    published: Optional[bool]

# この場合、最初に来るのがPostのRequest Parameter、次にPath Parameter、最後にQuery Parameterとなる。
@router.post('/new/{id}')
def create_blog(blog: BlogModel, id: int, version: int = 1):
    return {
        "data": blog,
        "id": id,
        "version": version
    }

# クエリパラメータに対するメタデータとして、Query()を使用する事で追加の情報を記入する事が出来る。
# また、エイリアスやdepreciatedも使用できる。Query(alias="commentId", deprecated=True)
@router.post('/new/{id}/comment')
def create_comment(blog: BlogModel, id: int, comment_id: int = Query(None, title="Id of Comment", description="some description for comment_id", alias="commentId")):
    return {
        'blog': blog,
        "id": id,
        "commnt_id": comment_id
    }

```

### Lecture27. Validator
```python
from fastapi import APIRouter
from pydantic import BaseModel

router = APIRouter(
    prefix='/blog',
    tags=['blog'],
)

class BlogModel(BaseModel):
    title: str
    content: str
    published: Optional[bool]

# この場合、最初に来るのがPostのRequest Parameter、次にPath Parameter、最後にQuery Parameterとなる。
@router.post('/new/{id}')
def create_blog(blog: BlogModel, id: int, version: int = 1):
    return {
        "data": blog,
        "id": id,
        "version": version
    }

# クエリパラメータのデフォルト値としてBodyを使用する事が出来る
# 加えて、クエリパラメータのデフォルト値をなしにしたい場合はBody(...)で実装出来る。
@router.post('/new/{id}/comment')
def create_comment(blog: BlogModel, id: int,
 comment_id: int = Query(None, title="Id of Comment", description="some description for comment_id", alias="commentId"),
 content: str = Body("hi how are you", min_length=10, max_length=20, regex="^[a-z\s]*$")):
    return {
        'blog': blog,
        "id": id,
        "comment_id": comment_id,
        "comment": content
    }

```

### Lecture28. Multiple values
- 複数値を取得するのは、クエリパラメーターのみ可能である。
```python
from fastapi import APIRouter
from pydantic import BaseModel

router = APIRouter(
    prefix='/blog',
    tags=['blog'],
)

class BlogModel(BaseModel):
    title: str
    content: str
    published: Optional[bool]

# この場合、最初に来るのがPostのRequest Parameter、次にPath Parameter、最後にQuery Parameterとなる。
@router.post('/new/{id}')
def create_blog(blog: BlogModel, id: int, version: int = 1):
    return {
        "data": blog,
        "id": id,
        "version": version
    }

# クエリパラメータのデフォルト値としてBodyを使用する事が出来る
# 加えて、クエリパラメータのデフォルト値をなしにしたい場合はBody(...)で実装出来る。
@router.post('/new/{id}/{comment_id}')
def create_comment(blog: BlogModel, id: int,
 comment_title: int = Query(None, title="Title of Comment", description="some description for comment_title", alias="commentTitle"),
 content: str = Body("hi how are you", min_length=10, max_length=20, regex="^[a-z\s]*$"),
 version: Optional[List[str]] = Query(None),
 comment_id: int = Path(..., gt=5, le=100)
 ):
    return {
        'blog': blog,
        "id": id,
        "comment_id": comment_id,
        "comment_title": comment_title,
        "comment": content,
        "version": version
    }

```

### Lecture29. Number validators
```python
```

### Lecture30. Complex subtypes
```python
```

## Section7. Database with SQLAlchemy
## Section8. Concepts
## Section9. Authentication
## Section10. Working with files
## Section11. Tasks
## Section12. More concepts
## Section13. Dependencies
## Section14. Instagram - FastAPI
## Section15. Instagram client
## Section16. Instagram client
## Section17. Conclusion