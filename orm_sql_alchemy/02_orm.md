
# `#ORM, Table Making Method, 

## [ORM_MAKE_TABLE](https://docs.sqlalchemy.org/en/20/orm/quickstart.html#create-objects-and-persist)


DDL (Data Definition Language) in SQLAlchemy ব্যবহার করে আপনি আপনার পাইথন প্রজেক্টে ডাটাবেস স্কিমা তৈরি, পরিবর্তন বা মুছে ফেলতে পারেন। DDL বলতে মূলত ডাটাবেস টেবিল, কলাম, ইনডেক্স, কনস্ট্রেইন্ট ইত্যাদি তৈরি বা পরিচালনার জন্য SQL কমান্ড যেমন `CREATE`, `ALTER`, `DROP` বোঝায়। SQLAlchemy এর মাধ্যমে এই কাজগুলো ORM (Object-Relational Mapping) এবং Core API ব্যবহার করে করা যায়। নিচে বাংলায় বিস্তারিতভাবে ব্যাখ্যা করা হলো কীভাবে আপনি আপনার পাইথন প্রজেক্টে SQLAlchemy দিয়ে DDL ব্যবহার করবেন।

---

### SQLAlchemy-তে DDL ব্যবহারের উপায়
SQLAlchemy-তে DDL দুটি প্রধান উপায়ে ব্যবহার করা যায়:
1. **ORM দিয়ে (Declarative Base)**: টেবিল এবং স্কিমা সংজ্ঞায়িত করতে পাইথন ক্লাস ব্যবহার করা, যা স্বয়ংক্রিয়ভাবে ডাটাবেসে টেবিল তৈরি করে।
2. **Core API দিয়ে (Table Object)**: সরাসরি `Table` অবজেক্ট এবং `schema` মডিউল ব্যবহার করে DDL স্টেটমেন্ট তৈরি এবং কাস্টমাইজ করা।

এছাড়াও, SQLAlchemy-তে `DDL` অবজেক্ট এবং কাস্টম SQL স্টেটমেন্ট ব্যবহার করে আরও নিয়ন্ত্রণ পাওয়া যায়।

---

### 1. ORM ব্যবহার করে DDL
ORM-এর মাধ্যমে টেবিল তৈরি করা সবচেয়ে সাধারণ এবং সহজ পদ্ধতি। এখানে আপনি পাইথন ক্লাস তৈরি করেন, যা ডাটাবেস টেবিলের সাথে ম্যাপ করা হয়। `Base.metadata.create_all()` মেথড ব্যবহার করে টেবিলগুলো ডাটাবেসে তৈরি করা হয়।

#### উদাহরণ: মুভি টেবিল তৈরি
ধরুন, আপনি একটি মুভি ডাটাবেস তৈরি করতে চান যেখানে `movies` টেবিল থাকবে।

```python
from sqlalchemy import create_engine, Column, Integer, String, Date
from sqlalchemy.orm import declarative_base, Session

# Declarative Base তৈরি
Base = declarative_base()

# Movie ক্লাস সংজ্ঞায়িত করা (DDL-এর সমতুল্য)
class Movie(Base):
    __tablename__ = 'movies'
    id = Column(Integer, primary_key=True)
    title = Column(String, nullable=False)
    release_date = Column(Date)
    genre = Column(String)

# ইঞ্জিন তৈরি
engine = create_engine("mysql+pymysql://root:@localhost/MOVIES")

# ডাটাবেসে টেবিল তৈরি
Base.metadata.create_all(engine)

# সেশন তৈরি করে ডাটা যোগ করা
with Session(engine) as session:
    new_movie = Movie(title="Inception", genre="Sci-Fi")
    session.add(new_movie)
    session.commit()
```

**ব্যাখ্যা**:
- **`Base = declarative_base()`**: এটি একটি বেস ক্লাস তৈরি করে, যার মাধ্যমে সব ORM ক্লাস সংজ্ঞায়িত হয়।
- **`__tablename__`**: ডাটাবেসে টেবিলের নাম নির্ধারণ করে।
- **`Column`**: টেবিলের কলাম সংজ্ঞায়িত করে, যেমন `id`, `title` ইত্যাদি।
- **`Base.metadata.create_all(engine)`**: সংজ্ঞায়িত সব টেবিল ডাটাবেসে তৈরি করে (যদি ইতিমধ্যে না থাকে)। এটি `CREATE TABLE` স্টেটমেন্টের সমতুল্য।
- এই পদ্ধতি স্বয়ংক্রিয়ভাবে `CREATE TABLE` স্টেটমেন্ট জেনারেট করে এবং ডাটাবেসে এক্সিকিউট করে।

**ফলাফল**:
ডাটাবেসে নিম্নলিখিত SQL-এর সমতুল্য কমান্ড চলবে:
```sql
CREATE TABLE movies (
    id INTEGER PRIMARY KEY,
    title VARCHAR NOT NULL,
    release_date DATE,
    genre VARCHAR
);
```

#### টেবিল ড্রপ করা
যদি আপনি টেবিল মুছতে চান:
```python
Base.metadata.drop_all(engine)  # DROP TABLE movies;
```

---

### 2. Core API ব্যবহার করে DDL
SQLAlchemy Core দিয়ে আপনি আরও নিম্ন-স্তরের নিয়ন্ত্রণ পান। এখানে `Table` অবজেক্ট এবং `schema` মডিউল ব্যবহার করে টেবিল তৈরি করা হয়। এটি ORM ছাড়াই সরাসরি DDL স্টেটমেন্ট তৈরি করতে ব্যবহৃত হয়।

#### উদাহরণ: Core API দিয়ে টেবিল তৈরি
```python
from sqlalchemy import create_engine, Table, Column, Integer, String, MetaData

# মেটাডেটা তৈরি
metadata = MetaData()

# টেবিল সংজ্ঞায়িত করা
movies = Table(
    'movies',
    metadata,
    Column('id', Integer, primary_key=True),
    Column('title', String, nullable=False),
    Column('genre', String)
)

# ইঞ্জিন তৈরি
engine = create_engine("mysql+pymysql://root:@localhost/MOVIES")

# টেবিল তৈরি
metadata.create_all(engine)

# ডাটা ইনসার্ট করা
with engine.connect() as conn:
    conn.execute(movies.insert().values(title="Inception", genre="Sci-Fi"))
    conn.commit()
```

**ব্যাখ্যা**:
- **`MetaData`**: টেবিল এবং অন্যান্য স্কিমা অবজেক্ট ধরে রাখে।
- **`Table`**: টেবিল সংজ্ঞায়িত করে, যা `CREATE TABLE` স্টেটমেন্টের সমতুল্য।
- **`metadata.create_all(engine)`**: ডাটাবেসে টেবিল তৈরি করে।
- **`engine.connect()`**: সরাসরি SQL কোয়েরি (যেমন INSERT) চালানোর জন্য ব্যবহৃত হয়।

**ফলাফল**:
একই `CREATE TABLE movies` স্টেটমেন্ট তৈরি হবে যা ORM উদাহরণে দেখানো হয়েছে।

#### টেবিল পরিবর্তন (ALTER)
Core API-তে `ALTER TABLE` স্টেটমেন্টের জন্য `schema.AlterTable` বা কাস্টম SQL ব্যবহার করা যায়। উদাহরণ:
```python
from sqlalchemy import schema

# নতুন কলাম যোগ করা
with engine.connect() as conn:
    conn.execute("ALTER TABLE movies ADD COLUMN release_year INTEGER")
    conn.commit()
```

---

### 3. কাস্টম DDL স্টেটমেন্ট
SQLAlchemy-তে `DDL` অবজেক্ট ব্যবহার করে কাস্টম DDL স্টেটমেন্ট তৈরি করা যায়। এটি উন্নত ক্ষেত্রে ব্যবহৃত হয়, যেমন ট্রিগার, ইনডেক্স, বা কাস্টম কনস্ট্রেইন্ট তৈরি।

#### উদাহরণ: ইনডেক্স তৈরি
```python
from sqlalchemy import create_engine, Table, Column, Integer, String, MetaData, schema

metadata = MetaData()
movies = Table(
    'movies',
    metadata,
    Column('id', Integer, primary_key=True),
    Column('title', String, nullable=False),
    Column('genre', String)
)

# ইনডেক্স তৈরি
index = schema.CreateIndex(schema.Index('idx_genre', movies.c.genre))

engine = create_engine("mysql+pymysql://root:@localhost/MOVIES")
metadata.create_all(engine)

# ইনডেক্স তৈরি করা
with engine.connect() as conn:
    conn.execute(index)
    conn.commit()
```

**ব্যাখ্যা**:
- **`schema.CreateIndex`**: ডাটাবেসে একটি ইনডেক্স তৈরি করে, যা `CREATE INDEX` স্টেটমেন্টের সমতুল্য।
- এটি `genre` কলামে দ্রুত সার্চ করার জন্য ইনডেক্স তৈরি করে।

---

### 4. আপনার প্রজেক্টে DDL ব্যবহারের টিপস
আপনার প্রজেক্টে (যেমন, মুভি ডাটাবেস) DDL ব্যবহার করার জন্য নিচের ধাপগুলো অনুসরণ করুন:

1. **ইঞ্জিন এবং সেশন তৈরি**:
   ```python
   from sqlalchemy import create_engine
   from sqlalchemy.orm import sessionmaker

   engine = create_engine("mysql+pymysql://root:@localhost/MOVIES")
   Session = sessionmaker(bind=engine)
   ```

2. **টেবিল সংজ্ঞায়িত করা**:
   ORM বা Core API ব্যবহার করে টেবিল সংজ্ঞায়িত করুন। উদাহরণস্বরূপ, আপনার মুভি ডাটাবেসে টেবিল তৈরি করতে:
   ```python
   from sqlalchemy.orm import declarative_base
   from sqlalchemy import Column, Integer, String, Date

   Base = declarative_base()

   class Movie(Base):
       __tablename__ = 'movies'
       id = Column(Integer, primary_key=True)
       title = Column(String, nullable=False)
       genre = Column(String)
       release_date = Column(Date)

   Base.metadata.create_all(engine)
   ```

3. **মাইগ্রেশনের জন্য Alembic ব্যবহার**:
   - সরাসরি DDL স্টেটমেন্ট লেখার পরিবর্তে, স্কিমা পরিবর্তনের জন্য **Alembic** ব্যবহার করা ভালো। Alembic হলো SQLAlchemy-এর জন্য একটি মাইগ্রেশন টুল যা টেবিল তৈরি, পরিবর্তন, বা মুছে ফেলার কাজ সহজ করে।
   - ইনস্টল করুন:
     ```bash
     pip install alembic
     ```
   - Alembic ইনিশিয়ালাইজ করুন:
     ```bash
     alembic init migrations
     ```
   - `alembic.ini` এবং `migrations/env.py` ফাইল কনফিগার করুন, এবং তারপর মাইগ্রেশন স্ক্রিপ্ট তৈরি করুন:
     ```bash
     alembic revision -m "create movies table"
     ```
   - মাইগ্রেশন স্ক্রিপ্টে টেবিল সংজ্ঞা যোগ করুন:
     ```python
     from alembic import op
     import sqlalchemy as sa

     def upgrade():
         op.create_table(
             'movies',
             sa.Column('id', sa.Integer, primary_key=True),
             sa.Column('title', sa.String, nullable=False),
             sa.Column('genre', sa.String),
             sa.Column('release_date', sa.Date)
         )

     def downgrade():
         op.drop_table('movies')
     ```
   - মাইগ্রেশন প্রয়োগ করুন:
     ```bash
     alembic upgrade head
     ```

4. **কাস্টম DDL স্টেটমেন্ট**:
   - যদি আপনার ট্রিগার, ভিউ, বা অন্যান্য কাস্টম স্কিমা অবজেক্ট তৈরি করতে হয়, তবে `engine.connect()` ব্যবহার করে সরাসরি SQL চালান:
     ```python
     with engine.connect() as conn:
         conn.execute("CREATE VIEW movie_view AS SELECT id, title FROM movies WHERE genre = 'Sci-Fi'")
         conn.commit()
     ```

5. **এরর হ্যান্ডলিং**:
   - DDL অপারেশন চালানোর সময় ত্রুটি হতে পারে (যেমন, টেবিল ইতিমধ্যে আছে)। তাই try-except ব্যবহার করুন:
     ```python
     try:
         Base.metadata.create_all(engine)
     except Exception as e:
         print(f"Error creating tables: {e}")
     ```

---

### আপনার প্রজেক্টে DDL ইন্টিগ্রেশন
আপনার প্রজেক্টে (যেমন, মুভি রিকমেন্ডেশন সিস্টেম) DDL ব্যবহার করতে নিচের পদক্ষেপগুলো অনুসরণ করুন:

1. **ডাটাবেস স্কিমা ডিজাইন**:
   - আপনার প্রয়োজনীয় টেবিল এবং কলাম সংজ্ঞায়িত করুন। উদাহরণস্বরূপ, `movies`, `genres`, `ratings` টেবিল।
   - কনস্ট্রেইন্ট যোগ করুন, যেমন প্রাইমারি কী, ফরেন কী, ইউনিক কনস্ট্রেইন্ট।

2. **ORM বা Core API বেছে নিন**:
   - যদি আপনি ORM ব্যবহার করেন, তবে ক্লাস তৈরি করে `Base.metadata.create_all()` ব্যবহার করুন।
   - যদি আরও নিয়ন্ত্রণ প্রয়োজন হয়, তবে Core API ব্যবহার করে `Table` অবজেক্ট তৈরি করুন।

3. **মাইগ্রেশন ম্যানেজমেন্ট**:
   - Alembic ব্যবহার করুন স্কিমা পরিবর্তনের জন্য। এটি প্রজেক্টের দীর্ঘমেয়াদী রক্ষণাবেক্ষণের জন্য গুরুত্বপূর্ণ।

### সতর্কতা এবং টিপস
1. **ইডেমপোটেন্সি**: `Base.metadata.create_all()` বা `metadata.create_all()` ইতিমধ্যে বিদ্যমান টেবিল পুনরায় তৈরি করার চেষ্টা করে না, তাই এটি নিরাপদ। তবে, কাস্টম DDL স্টেটমেন্টের ক্ষেত্রে ত্রুটি হ্যান্ডলিং যোগ করুন।
2. **মাইগ্রেশন**: সরাসরি DDL-এর পরিবর্তে Alembic ব্যবহার করুন, কারণ এটি স্কিমা পরিবর্তন ট্র্যাক করে এবং ডাউনগ্রেড করার সুবিধা দেয়।
3. **কানেকশন ম্যানেজমেন্ট**: ডাটাবেস কানেকশন সঠিকভাবে বন্ধ করুন (`engine.connect().close()` বা `with` স্টেটমেন্ট ব্যবহার করুন)।
4. **পারফরম্যান্স**: বড় স্কিমার ক্ষেত্রে ইনডেক্স এবং কনস্ট্রেইন্ট যোগ করুন কোয়েরি পারফরম্যান্স উন্নত করতে।
5. **সিকিউরিটি**: হার্ডকোডেড ক্রেডেনশিয়াল (`root:@`) এড়িয়ে পরিবেশ ভেরিয়েবল বা `.env` ফাইল ব্যবহার করুন।

---

### আপনার পূর্ববর্তী সমস্যার সাথে সম্পর্ক
আপনার পূর্ববর্তী প্রশ্নে উল্লেখিত `DataBaseTool`-এর সমস্যা (`groq.APIError`) DDL-এর সাথে সরাসরি সম্পর্কিত নয়, তবে যদি টেবিল সঠিকভাবে তৈরি না হয় বা স্কিমা অসঙ্গত থাকে, তবে এটি কোয়েরি ব্যর্থতার কারণ হতে পারে। নিশ্চিত করুন যে:
- `MOVIES` ডাটাবেসে `movies` টেবিল আছে এবং এর কলামগুলো (`id`, `title`) আপনার কোয়েরির সাথে মেলে।
- `Base.metadata.create_all(engine)` চালিয়ে টেবিল তৈরি করা হয়েছে।

---
