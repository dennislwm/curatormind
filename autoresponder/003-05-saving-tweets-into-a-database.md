## Saving tweets into a database

### TL;DR

In this section, we create a database called `twitter`. For every tweet, we save it to a table called `tweets`, while for every deleted tweet, we save it to a table called `deleted`.

### Developing a new feature with Test-Driven Development

1. Create a file `tests/test_model.py` and open it with your editor. We name our database `test.db` to avoid using the production database for our tests.

```
import pytest
from sqlalchemy import create_engine
from sqlalchemy.orm import Session

@pytest.fixture(scope="session")
def engine():
  return create_engine("sqlite:///test.db", echo=True)

def test_tweets(engine):
  pass
```

Run `make` and it should fail because you haven't installed the package `sqlalchemy`. Type the following command to install it.

```
pipenv install sqlalchemy
```

Run `make` again and your first test has passed! You have created an SQLite database `test.db` in the current directory, and returned the database `engine` as a parameter to your function `test_tweets()`.

2. Open the file `tests/test_model.py`.

* insert an import line.
* insert a new fixture function.
* change the parameter of `test_tweets` to `tables` previously.

```
# ... previous imports

from tweetlab import model

# ... previous def engine()

@pytest.fixture(scope="session")
def tables(engine):
  model.base.metadata.create_all(engine)
  yield
  model.base.metadata.drop_all(engine)

def test_tweets(tables):
  pass
```

Run `make` and it should fail because you haven't defined your custom model.

The line `yield` allows any tests to be executed before continuing with rest of code.

3. Create a file `src/tweetlab/model.py` and open it with your editor.

```
from sqlalchemy.ext.declarative import declarative_base
import sqlalchemy as db

base = declarative_base()

class Tweets(base):
  __tablename__ = 'tweets'
  id            = db.Column(db.Integer, primary_key=True)
  user_id       = db.Column(db.String(20), nullable=False)
  text          = db.Column(db.String(280), nullable=False)

class Deleted(base):
  __tablename__ = 'deleted'
  id            = db.Column(db.Integer, primary_key=True)
  user_id       = db.Column(db.String(20), nullable=False)
  text          = db.Column(db.String(280), nullable=False)
```

We create a module variable `base` and assign it to the return value of `declaration_base()`, which is a function from the `sqlalchemy` package.

With `base`, we can create and map a custom Python class to a table in our relational database. This table can be either an existing one, or in our case, a new table.

For our Python class `Tweets`, we declare a required variable `__tablename__` and set it to our table name `tweets`. Then we define three Python class variables:
1. `id` - a database column of type Integer and primary key
2. `user_id` - a database column of type String(20) and non-nullable
3. `text` - a database column of type String(280) and non-nullable

The purpose of the three database columns are as follows:
1. Primary key `id` is for creating relations with other tables.
2. `user_id` is used to store a Twitter user id, a unique 64-bit unsigned integer
3. `text` is used to store a tweet, with a maximum length of 280 chars

The Python class `Deleted` is similar to the class `Tweets`, except that its purpose is to save deleted tweets.

Run `make` and your test has passed! You have created your tables within the SQLite database `test.db`.

> In order to see the tables within your database, open the `test.db` in an SQLite editor, such as DB Browser (SQLite).

Open the file `test_model.py` and comment out the line `model.base.metadata.drop_all(engine)` to prevent destroying the tables after tests have finished executing. This is to facilitate keeping all tables and records during the test.

4. Open the file `tests/test_model.py`.

* insert an import line.
* insert a new fixture function.
* append two test functions (replace the existing `test_tweets()`).

```
# ... previous imports

from tweetlab.model import insert_tweet, insert_deleted

# ... previous pytest.fixtures

@pytest.fixture
def session(engine, tables):
  """
  Returns an sqlalchemy session, and 
  after that tears down everything properly.
  """
  connection = engine.connect()
  transaction = connection.begin()
  session = Session(bind=connection)
  yield session
  session.close()
  transaction.rollback()
  connection.close()

def test_tweets(session):
  result = insert_tweet(session, "1", "this is a tweet")
  for row in result:
    print(f"user_id: {row.user_id}")
    print(f"text: {row.text}")

def test_deleted(session):
  result = insert_deleted(session, "2", "this is a deleted tweet")
  for row in result:
    print(f"user_id: {row.user_id}")
    print(f"text: {row.text}")
```

Run `make` and it should fail because you haven't defined your module functions `insert_tweet()` and `insert_deleted()`.

Again, the line `yield session` allows any tests to be executed before continuing with rest of code.

4. Open the file `src/tweetlab/model.py`.

* append two module functions.

```
# ... previous class declarations

def insert_tweet(session, str_user_id, str_text):
  assert(session)
  assert(str_user_id)
  assert(str_text)

  obj_tweet = Tweets(
    user_id = str_user_id, 
    text    = str_text
  )
  session.add(obj_tweet)
  session.commit()
  return session.query(Tweets).filter(Tweets.user_id==str_user_id).filter(Tweets.text==str_text)

def insert_deleted(session, str_user_id, str_text):
  assert(session)
  assert(str_user_id)
  assert(str_text)

  obj_tweet = Deleted(
    user_id = str_user_id, 
    text    = str_text
  )
  session.add(obj_tweet)
  session.commit()
  return session.query(Deleted).filter(Deleted.user_id==str_user_id).filter(Deleted.text==str_text)
```

Run `make` and your both your tests have passed! You have inserted a tweet each into both tables `tweets` and `deleted` within the SQLite database `test.db`.

Alternatively, run `make verbose` to see the output of the tests.
