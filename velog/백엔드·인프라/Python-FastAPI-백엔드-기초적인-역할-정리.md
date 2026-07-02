---
title: "[Python + FastAPI] 백엔드 기초적인 역할 정리"
tags: ["FastAPI", "Python3", "백엔드 역할"]
date: 2024-06-13
velog_id: 64382a6b-ed28-4712-9865-9076c98eeba3
velog_url: https://velog.io/@steela/Python-FastAPI-백엔드-기초적인-역할-정리
velog_updated: 2026-07-02T06:23:02.195Z
synced_at: 2026-07-02
---

> 🔗 원본: [velog.io/@steela/Python-FastAPI-백엔드-기초적인-역할-정리](https://velog.io/@steela/Python-FastAPI-백엔드-기초적인-역할-정리) · 📅 2024-06-13
Udemy FastAPI 강의를 무작정 따라하면서 백엔드의 역할을 대충이나마 정리하려한다. 무작정 따라만 할 때는 흐름과 손에 나를 맡겼지만 어느 정도 진도가 나가며 코드별로 연결고리를 머릿속에 저장해두고 싶었다.

일단 글쓴이는 Javascript, Typescript, React, Next.js 사용에 익숙한 프론트엔드 개발자여서 관련 용어에 비유하듯이 설명하는 경향이 있을 수 있다.

## 폴더구조
![](https://velog.velcdn.com/images/steela/post/d4c8a444-f2dd-4439-9b97-aab5c25deffa/image.png)



크게 나눠보자면,
>   1. main
  2. database
  3. config
  4. models
  5. routers
  
로 정리하려한다.

<br />

### 1. main
**main.py**에서는 FastAPI와 router, database를 연결하고 그 연결의 시작과 끝을 설정하는 lifespan을 설정해준다.
```
from contextlib import asynccontextmanager
from fastapi import FastAPI
from storeapi.database import database
from storeapi.routers.post import router as post_router

@asynccontextmanager
async def lifespan(app: FastAPI):
    await database.connect()
    yield
    await database.disconnect()

app = FastAPI(lifespan=lifespan)
app.include_router(post_router)

```

<br />

### 2. database
**database.py**에서는 테이블을 만든다. 즉 db에 들어갈 내용의 구조와 형식을 구성한다.
```
post_table = sqlalchemy.Table(
    "posts",
    metadata,
    sqlalchemy.Column("id", sqlalchemy.Integer, primary_key=True),
    sqlalchemy.Column("body", sqlalchemy.String),
)

comment_table = sqlalchemy.Table(
    "comments",
    metadata,
    sqlalchemy.Column("id", sqlalchemy.Integer, primary_key=True),
    sqlalchemy.Column("body", sqlalchemy.String),
    sqlalchemy.Column("post_id", sqlalchemy.ForeignKey("posts.id"), nullable=False),
)
```

<br />

### 3. config
**config.py**에서는 설정한 환경변수를 가져온다. 이 때 타입안내를 해주어야한다.

```
from functools import lru_cache
from typing import Optional

from pydantic_settings import BaseSettings


class BaseConfig(BaseSettings):
    ENV_STATE: Optional[str] = None

    class Config:
        env_file: str = ".env"
        extra = "allow"


class GlobalConfig(BaseConfig):
    DATABASE_URL: Optional[str] = None
    DB_FORCE_ROLL_BACK: bool = False


class DevConfig(GlobalConfig):
    class Config:
        env_prefix = "DEV_"
        
@lru_cache()
def get_config(env_state: str):
    configs = {"dev": DevConfig, "prod": ProdConfig, "test": TestConfig}
    return configs[env_state]()


config = get_config(BaseConfig().ENV_STATE)
```

<br />

### 4. models
**models 폴더**에서는 그 속에 post.py와 같이 post와 관련된 파일을 따로 생성하여 dict로 들어갈 id와 value값이 타입을 설정해준다.
**[models/post.py]**

```
from pydantic import BaseModel


class UserPostIn(BaseModel):
    body: str


class UserPost(UserPostIn):
    id: int


class CommentIn(BaseModel):
    body: str
    post_id: int


class Comment(CommentIn):
    id: int


class UserPostWithComments(BaseModel):
    post: UserPost
    comments: list[Comment] = []


"""
{
    "post": {"id": 0, "body": "This is a post"},
    "comment": [{"id": 2, "post_id": 0, "body": "This is a comment"}]
}
"""

```

<br />

### 4. routers
**routers 폴더**에서는 그 속에 post.py와 같이 post와 관련된 파일을 따로 생성하여 클라이언트단에서 요청할 url주소와 return값 등을 작성하여 api를 구현한다.
**[routers/post.py]**
```
from fastapi import APIRouter, HTTPException

from storeapi.database import comment_table, database, post_table
from storeapi.models.post import (
    Comment,
    CommentIn,
    UserPost,
    UserPostIn,
    UserPostWithComments,
)

router = APIRouter()


async def find_post(post_id: int):
    query = post_table.select().where(post_table.c.id == post_id)
    return await database.fetch_one(query)  # fetch_one / fetch_all


@router.post("/post", response_model=UserPost, tags=["posts"])
async def create_post(post: UserPostIn):
    data = post.dict()
    query = post_table.insert().values(data)
    last_record_id = await database.execute(query)
    await database.execute(query)
    return {**data, "id": last_record_id}


@router.get("/post", response_model=list[UserPost], tags=["posts"])
async def get_all_posts():
    query = post_table.select()
    return await database.fetch_all(query)


@router.post("/comment", response_model=Comment, status_code=201, tags=["posts"])
async def create_comment(comment: CommentIn):
    post = await find_post(comment.post_id)
    if not post:
        raise HTTPException(status_code=404, detail="Post not found")

    data = comment.dict()
    query = comment_table.insert().values(data)
    last_record_id = await database.execute(query)
    return {**data, "id": last_record_id}


@router.get("/post/{post_id}/comment", response_model=list[Comment], tags=["posts"])
async def get_comments_on_post(post_id: int):
    query = comment_table.select().where(comment_table.c.post_id == post_id)
    return await database.fetch_all(query)


@router.get("/post/{post_id}", response_model=UserPostWithComments, tags=["posts"])
async def get_post_with_comments(post_id: int):
    post = find_post(post_id)
    if not post:
        raise HTTPException(status_code=404, detail="Post not found")
    return {
        "post": post,
        "comments": await get_comments_on_post(post_id),
    }

```
