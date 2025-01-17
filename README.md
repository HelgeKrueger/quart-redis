# Quart-Redis
[![Documentation Status](https://readthedocs.org/projects/quart-redis/badge/?version=latest)](https://quart-redis.readthedocs.io/en/latest/)
![PyPI](https://img.shields.io/pypi/v/quart-redis)
![PyPI - Python Version](https://img.shields.io/pypi/pyversions/quart-redis)
![PyPI - Downloads](https://img.shields.io/pypi/dm/quart-redis)
![GitHub](https://img.shields.io/github/license/enchant97/quart-redis)
![GitHub issues](https://img.shields.io/github/issues/enchant97/quart-redis)
![GitHub last commit](https://img.shields.io/github/last-commit/enchant97/quart-redis)

An easy way of setting up a redis connection in quart.

## Requirements
- quart >= 0.18
- redis >= 4.2

## Example of Use
```
pip install quart-redis
```

```python
from quart import Quart
from quart_redis import RedisHandler, get_redis

app = Quart(__name__)
app.config["REDIS_URI"] = "redis://localhost"
# override default connection attempts, set < 0 to disable
# app.config["REDIS_CONN_ATTEMPTS"] = 3
redis_handler = RedisHandler(app)

@app.route("/")
async def index():
    redis = get_redis()

    val = await redis.get("my-key")

    if val is None:
        await redis.set("my-key", "it works!")
        val = await redis.get("my-key")

    return val
```

## Example of a test

Due to quart_redis using before_serving and after_serving, the following pattern needs to be used to test the above code.

```python
import pytest
from app import app

@pytest.fixture(name="my_app", scope="function")
async def _my_app():
    async with app.test_app() as test_app:
        yield test_app

async def test_redis(my_app):
    async with my_app.test_client() as client:
        result = await client.get("/")
        assert await result.data == b"it works!"
```
