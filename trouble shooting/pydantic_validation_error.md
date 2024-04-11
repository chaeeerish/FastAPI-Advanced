# pydantic.error_wrappers.ValidationError

### 에러가 발생한 지점
question_router.py
```python
@router.get("/list", response_model=question_schema.QuestionList)
def question_list(db: Session = Depends(get_db), page: int = 0, size: int = 10):
    total, _question_list = question_crud.get_question_list(db, skip=page*size, limit=size)
    return {
        'total': total,
        'question_list': _question_list
    }

@router.get("/detail/{question_id}", response_model=question_schema.Question)
def question_detail(question_id: int, db: Session = Depends(get_db)):
    question = question_crud.get_question(db, question_id=question_id)
    return question
```

**response_model을 이용하여 반환하는 라우터 함수에서 에러가 발생하는 것 같다.**

### Error Message
```
INFO:     127.0.0.1:56059 - "GET /api/question/list?page=0&size=10 HTTP/1.1" 500 Internal Server Error
ERROR:    Exception in ASGI application
Traceback (most recent call last):
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/uvicorn/protocols/http/httptools_impl.py", line 435, in run_asgi
    result = await app(  # type: ignore[func-returns-value]
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/uvicorn/middleware/proxy_headers.py", line 78, in __call__
    return await self.app(scope, receive, send)
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/fastapi/applications.py", line 276, in __call__
    await super().__call__(scope, receive, send)
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/starlette/applications.py", line 122, in __call__
    await self.middleware_stack(scope, receive, send)
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/starlette/middleware/errors.py", line 184, in __call__
    raise exc
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/starlette/middleware/errors.py", line 162, in __call__
    await self.app(scope, receive, _send)
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/starlette/middleware/cors.py", line 84, in __call__
    await self.app(scope, receive, send)
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/starlette/middleware/exceptions.py", line 79, in __call__
    raise exc
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/starlette/middleware/exceptions.py", line 68, in __call__
    await self.app(scope, receive, sender)
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/fastapi/middleware/asyncexitstack.py", line 21, in __call__
    raise e
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/fastapi/middleware/asyncexitstack.py", line 18, in __call__
    await self.app(scope, receive, send)
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/starlette/routing.py", line 718, in __call__
    await route.handle(scope, receive, send)
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/starlette/routing.py", line 276, in handle
    await self.app(scope, receive, send)
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/starlette/routing.py", line 66, in app
    response = await func(request)
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/fastapi/routing.py", line 255, in app
    content = await serialize_response(
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/fastapi/routing.py", line 141, in serialize_response
    raise ValidationError(errors, field.type_)
pydantic.error_wrappers.ValidationError: 10 validation errors for QuestionList
response -> question_list -> 0
  value is not a valid dict (type=type_error.dict)
response -> question_list -> 1
  value is not a valid dict (type=type_error.dict)
response -> question_list -> 2
  value is not a valid dict (type=type_error.dict)
response -> question_list -> 3
  value is not a valid dict (type=type_error.dict)
response -> question_list -> 4
  value is not a valid dict (type=type_error.dict)
response -> question_list -> 5
  value is not a valid dict (type=type_error.dict)
response -> question_list -> 6
  value is not a valid dict (type=type_error.dict)
response -> question_list -> 7
  value is not a valid dict (type=type_error.dict)
response -> question_list -> 8
  value is not a valid dict (type=type_error.dict)
response -> question_list -> 9
  value is not a valid dict (type=type_error.dict)
```

### Try 1
answer_schema.py
```python
class Answer(BaseModel):
    id: int
    content: str
    create_date: datetime.datetime

    class Config:
        orm_mode = True
```

question_schema.py
```python
class Question(BaseModel):
    id: int
    subject: str
    content: str
    create_date: datetime.datetime
    answers: list[Answer] = []

    class Config:
        orm_mode = True
```

**class Config를 추가해주니까 해결되었다!** (참고하던 책에 주의 사항으로 적혀 있었음...ㅋㅋ)

### orm_mode

구 버전인 pydantic V1을 사용할 경우에는 다음과 같은 오류가 발생한다.
```
pydantic.error_wrappers.ValidationError: 1 validation error for Question
response -> 0
  value is not a valid dict (type=type_error.dict)
```

왜냐하면, 리턴값에 해당하는 _question_list의 요소값이 딕셔너리가 아닌 Question 모델이기 때문이다.
Question 모델은 Question 스키마로 자동으로 변환되지 않는다. 하지만, Question 스키마에 다음처럼 orm_mode 항목을 True로 설정하면 Question 모델의 항목들이 자동으로 Question 스키마로 매핑된다.
