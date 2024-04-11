# cannot import name 'field_validator' from 'pydantic'

### 에러가 발생한 지점
answer_schema.py
```python
import datetime

from pydantic import BaseModel, field_validator

class AnswerCreate(BaseModel):
    content: str

    @field_validator('content')
    def not_empty(cls, v):
        if not v or not v.strip():
            raise ValueError('빈 값은 허용되지 않습니다.')
        return v

class Answer(BaseModel):
    id: int
    content: str
    create_date: datetime.datetime
```

### Error Message
```
Traceback (most recent call last):
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/bin/uvicorn", line 8, in <module>
    sys.exit(main())
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/click/core.py", line 1130, in __call__
    return self.main(*args, **kwargs)
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/click/core.py", line 1055, in main
    rv = self.invoke(ctx)
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/click/core.py", line 1404, in invoke
    return ctx.invoke(self.callback, **ctx.params)
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/click/core.py", line 760, in invoke
    return __callback(*args, **kwargs)
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/uvicorn/main.py", line 410, in main
    run(
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/uvicorn/main.py", line 578, in run
    server.run()
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/uvicorn/server.py", line 61, in run
    return asyncio.run(self.serve(sockets=sockets))
  File "/Users/chaeeerish/.pyenv/versions/3.10.13/lib/python3.10/asyncio/runners.py", line 44, in run
    return loop.run_until_complete(main)
  File "uvloop/loop.pyx", line 1517, in uvloop.loop.Loop.run_until_complete
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/uvicorn/server.py", line 68, in serve
    config.load()
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/uvicorn/config.py", line 473, in load
    self.loaded_app = import_from_string(self.app)
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/uvicorn/importer.py", line 24, in import_from_string
    raise exc from None
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/uvicorn/importer.py", line 21, in import_from_string
    module = importlib.import_module(module_str)
  File "/Users/chaeeerish/.pyenv/versions/3.10.13/lib/python3.10/importlib/__init__.py", line 126, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "<frozen importlib._bootstrap>", line 1050, in _gcd_import
  File "<frozen importlib._bootstrap>", line 1027, in _find_and_load
  File "<frozen importlib._bootstrap>", line 1006, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 688, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 883, in exec_module
  File "<frozen importlib._bootstrap>", line 241, in _call_with_frames_removed
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/main.py", line 4, in <module>
    from domain.answer import answer_router
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/domain/answer/answer_router.py", line 6, in <module>
    from domain.answer import answer_schema, answer_crud
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/domain/answer/answer_schema.py", line 3, in <module>
    from pydantic import BaseModel, field_validator
ImportError: cannot import name 'field_validator' from 'pydantic' (/Users/chaeeerish/Documents/GitHub/fast-api-4/myenv/lib/python3.10/site-packages/pydantic/__init__.cpython-310-darwin.so)
```

### Try 1
Pydantic은 설치되어 있는데, field_validator를 못찾는 것 같다.

python 11 환경에서 실행했을 때는 문제가 없었는데, python 10 환경으로 지정해주니 문제가 발생했다.

그렇다면? => pydantic 라이브러리의 버전을 최신으로 변경하려고 시도하였다. 하지만, 다음과 같은 에러 메시지를 만났다.
```
ERROR: pip's dependency resolver does not currently take into account all the packages that are installed. This behaviour is the source of the following dependency conflicts.
fastapi 0.95.1 requires pydantic!=1.7,!=1.7.1,!=1.7.2,!=1.7.3,!=1.8,!=1.8.1,<2.0.0,>=1.6.2, but you have pydantic 2.0 which is incompatible.
```

따라서, 1.6.2 버전으로 변경하였다. (1.10.2 버전에서도 같은 에러가 발생한다.)
```
pip install pydantic==1.6.2
myenv/bin/uvicorn main:app
```

### Try 2 (Success)
field_validator 대신에 validator 어노테이션으로 수정하였다.
```python
from pydantic import BaseModel, validator

class QuestionCreate(BaseModel):
    subject: str
    content: str

    @validator('subject', 'content')
    def not_empty(cls, v):
        if not v or not v.strip():
            raise ValueError('빈 값은 허용되지 않습니다.')
        return v
```

그랬더니,,, 이번에는 다른 곳에서 에러가 발생하였다.

To be continued..

[pydantic_validaton_error.md](pydantic_validation_error.md)