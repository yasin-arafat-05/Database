
<br>

# `# Connection of postgrestsql with sqlalchemy:`

<br>

```python
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession
from sqlalchemy.orm import sessionmaker, declarative_base

# Async URL for PostgreSQL
DATABASE_URL = "postgresql+asyncpg://myuser:mypassword@localhost/mydb"

# Create async engine
engine = create_async_engine(
    DATABASE_URL,
    echo=True,     
)

# Async session
async_session = sessionmaker(
    engine, class_=AsyncSession, expire_on_commit=False
)

# Base class for models
Base = declarative_base()

```

