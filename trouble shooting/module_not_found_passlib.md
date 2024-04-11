# ModuleNotFoundError: No module named 'passlib'

### 에러가 발생한 지점
user_crud.py
```python
from passlib.context import CryptContext
from sqlalchemy.orm import Session
from domain.user.user_schema import UserCreate
from models import User

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

def create_user(db: Session, user_create: UserCreate):
    db_user = User(username=user_create.username,
                   password=pwd_context.hash(user_create.password1),
                   email=user_create.email)
    db.add(db_user)
    db.commit()

def get_existing_user(db: Session, user_create: UserCreate):
    return db.query(User).filter(
        (User.username == user_create.username) | (User.email == user_create.email)
    ).first()
```

## Error Message
```
Process SpawnProcess-1:
Traceback (most recent call last):
  File "/opt/homebrew/Cellar/python@3.11/3.11.8/Frameworks/Python.framework/Versions/3.11/lib/python3.11/multiprocessing/process.py", line 314, in _bootstrap
    self.run()
  File "/opt/homebrew/Cellar/python@3.11/3.11.8/Frameworks/Python.framework/Versions/3.11/lib/python3.11/multiprocessing/process.py", line 108, in run
    self._target(*self._args, **self._kwargs)
  File "/opt/homebrew/lib/python3.11/site-packages/uvicorn/_subprocess.py", line 78, in subprocess_started
    target(sockets=sockets)
  File "/opt/homebrew/lib/python3.11/site-packages/uvicorn/server.py", line 62, in run
    return asyncio.run(self.serve(sockets=sockets))
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/opt/homebrew/Cellar/python@3.11/3.11.8/Frameworks/Python.framework/Versions/3.11/lib/python3.11/asyncio/runners.py", line 190, in run
    return runner.run(main)
           ^^^^^^^^^^^^^^^^
  File "/opt/homebrew/Cellar/python@3.11/3.11.8/Frameworks/Python.framework/Versions/3.11/lib/python3.11/asyncio/runners.py", line 118, in run
    return self._loop.run_until_complete(task)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/opt/homebrew/Cellar/python@3.11/3.11.8/Frameworks/Python.framework/Versions/3.11/lib/python3.11/asyncio/base_events.py", line 654, in run_until_complete
    return future.result()
           ^^^^^^^^^^^^^^^
  File "/opt/homebrew/lib/python3.11/site-packages/uvicorn/server.py", line 69, in serve
    config.load()
  File "/opt/homebrew/lib/python3.11/site-packages/uvicorn/config.py", line 458, in load
    self.loaded_app = import_from_string(self.app)
                      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/opt/homebrew/lib/python3.11/site-packages/uvicorn/importer.py", line 24, in import_from_string
    raise exc from None
  File "/opt/homebrew/lib/python3.11/site-packages/uvicorn/importer.py", line 21, in import_from_string
    module = importlib.import_module(module_str)
             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/opt/homebrew/Cellar/python@3.11/3.11.8/Frameworks/Python.framework/Versions/3.11/lib/python3.11/importlib/__init__.py", line 126, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "<frozen importlib._bootstrap>", line 1204, in _gcd_import
  File "<frozen importlib._bootstrap>", line 1176, in _find_and_load
  File "<frozen importlib._bootstrap>", line 1147, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 690, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 940, in exec_module
  File "<frozen importlib._bootstrap>", line 241, in _call_with_frames_removed
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/main.py", line 6, in <module>
    from domain.user import user_router
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/domain/user/user_router.py", line 7, in <module>
    from domain.user import user_crud, user_schema
  File "/Users/chaeeerish/Documents/GitHub/fast-api-4/domain/user/user_crud.py", line 1, in <module>
    from passlib.context import CryptContext
ModuleNotFoundError: No module named 'passlib'
```

### Try 1
```
pip install passlib==1.7.4
```
```
pip install -r requirements.txt
```

분명히 설치가 되어있다.
```
chaeeerish@yangchaelin-ui-MacBookAir fast-api-4 % python
Python 3.10.13 (main, Mar  8 2024, 00:32:01) [Clang 14.0.3 (clang-1403.0.22.14.1)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import passlib
```
이렇게 하면, 에러가 안나기 때문에!

### Try 2
가만히 보니,,
/opt/homebrew/Cellar/python@3.11/3.11.8/Frameworks/Python.framework/Versions/3.11/lib/python3.11/multiprocessing/process.py", line 314, in _bootstrap
    self.run()

/opt/homebrew/~ 경로에서 라이브러리를 찾네?

원래 이러나?

가상환경에서 찾아야 하는거 아닌가?

이렇게 실행해보자.
```myenv/bin/uvicorn main:app```

오? 그랬더니 다른 에러가 나온다.
```
(myenv) chaeeerish@yangchaelin-ui-MacBookAir fast-api-4 % myenv/bin/uvicorn main:app
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

그렇다면 다음 페이지로 가보자..