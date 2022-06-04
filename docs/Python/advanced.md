# Python Advanced

## How do I migrate my database solution with python?

Use Alembic python library to do that.

``` bash
alembic init ./alembic
```

or for async alembic

``` bash
alembic init -t async ./alembic
```

After that we need to fill the config alembic.ini in ./.

First we need to remove one line:

```conf
sqlalchemy.url = driver://user:pass@localhost/dbname
```

Second, we need to add an __init__.py file in the ./alembic/ folder and alter the ./alembic/env.py file:

- First alter the import engine_from_config to your used engine (like create_engine)
- add your logger instead of logging.config (like from app.logger import logger) + comment "fileConfig(config.config_file_name)"
- remove from sqlalchemy import pool

Import your base class

``` py
from app.db.base import Base
    target_metadata = Base.metadata
```

import your url function

``` py
from app.db.get_url import get_url
```

Use get url function instead of

``` py
config.get_main_option("sqlalchemy.url")
```

add ", compare_type=True"

now you are ready to create revisions:

``` bash
alembic revision --autogenerate -m "{{ DESCRIPTION }}"
```

Sources:

- <https://alembic.sqlalchemy.org/en/latest/autogenerate.html>
- <https://alembic.sqlalchemy.org/en/latest/cookbook.html#using-asyncio-with-alembic>

## How to use type hints for return of "self" in classes?

``` py
from __future__ import annotations
```

Sources:

- <https://stackoverflow.com/questions/33533148/how-do-i-type-hint-a-method-with-the-type-of-the-enclosing-class>

## SQLALchemy 2.0

### Result Object and Mappings with column names

``` py
result = (await db.execute(time_aggregation_select)).mappings().all()
```

Sources:

- <https://docs.sqlalchemy.org/en/14/core/connections.html#sqlalchemy.engine.Result>
