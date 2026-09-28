# Pytest documentation

This file presents pytest and how you can use it to build a framework of test in python.

## Installation/Setup

Installation via pip :
``` bash
 pip install pytest
```


Requirement: Python 3.10+ (ou PyPy 3).

## Writing a Test & Assertion

A pytest test is a function that has a name starting with test_ , in a file called test_*.py or *_test.py :

### Setup
- main.py holds the code to test. 
Example: a get_weather(temp) function that returns "hot" if temp > 25, otherwise "cold".
- Create one test file per module, named test_ + the module name (here test_main.py). The test_ prefix matters because pytest looks for it.
### Writing the test in test_main.py:

from main import get_weather
```python
def test_get_weather():
    assert get_weather(27) == "hot"
```
    
- Import the code you want to test and write it a function whose name starts with test_.
- Inside it, use assert <condition>. The condition must be true or false.
True: the test passes.
False: the test fails.
You can put several assert statements in one test

### Running it
- From the terminal, in the folder containing your code, run pytest test_main.py. Use pytest, not python pytest.
pytest reports how many tests it found, how many passed, and how long it took.

PS: Normally you have one test file per module, though you can also test individual functions.


## Unit tests

- Smallest type of test: one function / method (sometimes a class)
- Goal: check a small unit gives the expected result
- Benefit: when something breaks, the failing test isolates where
- Protects against accidentally breaking things during development
- Other types exist: integration, system, end-to-end (different purposes)

### More assertions

```python
import pytest
from main import add, divide

def test_add():
    assert add(2, 3) == 5, "2 + 3 should be 5"
    assert add(-1, 1) == 0, "-1 + 1 should be 0"
    assert add(100, 0) == 100

def test_divide():
    with pytest.raises(ValueError, match="Cannot divide by zero"):
        divide(1, 0)
```

- Cover **edge cases**: empty inputs, negatives, odd values, not just the expected case
- Optional message after the assert: `assert x == y, "description"` → shown on failure
- Testing exceptions: `pytest.raises(ExceptionType, match="...")`
  - `match` is a **regex** applied to the error message
  - wrong pattern → failure: "pattern did not match"

### On more example:
```python
 def test_addition():
     assert 1 + 1 == 2
 def test_liste():
     resultat = [1, 2, 3]
     assert 3 in resultat
     assert len(resultat) == 3
```
### Test an exception using pytest.raises :
```python
 import pytest
 def test_division_par_zero():
     with pytest.raises(ZeroDivisionError):
1/0
```
### Test a warning using pytest.warns :
```python
 def test_warning():
     with pytest.warns(UserWarning):
         warnings.warn("attention", UserWarning)
```



## Fixtures (setup)

Fixture is a code that runs **before each test that gives a fresh instance / fresh data. It's declared with `@pytest.fixture`; injected by putting its name as a test parameter.
It's mainly used when tests must run in isolation, not depend on run order or on state left by another test

```python
import pytest
from main import UserManager

@pytest.fixture
def user_manager():
    return UserManager()          # new instance for every test

def test_add_user(user_manager):
    user_manager.add_user("John Doe")      # illustrative
    ...

def test_add_duplicate_user(user_manager):
    user_manager.add_user("John Doe")
    ...                                    # adding again must be refused
```

- Without the fixture (one global `UserManager()` shared by all tests):
  - 1st test adds "John Doe" and nothing clears it
  - 2nd test (duplicate) then **fails** because the user already exists
  - = one test's side effects leaking into another

## Fixtures (teardown)

- `yield` in a fixture:
  - code **before/at** `yield` = setup (runs before the test)
  - code **after** `yield` = teardown / cleanup (runs after the test)
This is useful for real resources: clear a DB, delete a file, close a connection.

```python
@pytest.fixture
def db():
    database = Database()     # setup (in-memory in the example)
    yield database            # test runs here
    database.clear()          # teardown
```

- Example tests on the DB: add user, add duplicate user, delete user

## Useful built-in fixtures

- `tmp_path`: a unique temporary directory per test (a `pathlib.Path` object)
- `capsys`: captures `stdout` / `stderr`
- `monkeypatch`: modifies attributes, environment variables or dictionaries, reversibly
- `request`: access to metadata about the current test

### `conftest.py`

- Special file for fixtures shared across several test files in the same directory
- No explicit import needed
  
## Scope
Scope controls how often to create a fixture: function, class , module , package , session (once per run) :
'''python
 @pytest.fixture(scope="module")
 def connexion_db():
     conn = creer_connexion()
     yield conn
     conn.close()
'''

yield for the teardown: everything that comes after yield runs after the test even if there is an error


## Parametrized tests

- Avoids repeating the same assert with different values
- `@pytest.mark.parametrize("names", [list of tuples])`
- Parameter names in the decorator must match the test function's arguments
- The test runs once per tuple, each reported separately

```python
import pytest
from main import is_prime

@pytest.mark.parametrize("num, expected", [
    (2, True),
    (7, True),
    (9, False),
    (1, False),
])                                 # illustrative values
def test_is_prime(num, expected):
    assert is_prime(num) == expected
```

- If one case fails, output is e.g. "1 failed, 7 passed" and it says which case failed
- Any number of parameters is fine (`num1, num2, expected`...)
- Good when tests are repetitive and only inputs/outputs change



## Mocks

A mock is a fake version of a dependency that returns controlled data
As a unit test should test one thing; an external API being down, needing a key or changing must not make my test fail, and here comes the need to use a mock.

This needs the `pytest-mock` plugin (`pip install pytest-mock`) → gives the `mocker` fixture

### Mock a function 

Code under test: `get_weather()` calls `requests.get(...)`; status 200 → return JSON, otherwise raise a custom error.

```python
def test_get_weather(mocker):
    mock_get = mocker.patch("main.requests.get")
    mock_get.return_value.status_code = 200
    mock_get.return_value.json.return_value = {"temperature": 25, "condition": "sunny"}

    result = get_weather()

    assert result == {"temperature": 25, "condition": "sunny"}
    mock_get.assert_called_once_with(...)   # the expected URL / args
```

- `mocker.patch("module.thing")` replaces it with a mock for the duration of the test
- Set what it returns with `.return_value`
- `json` is a function, so its result is `.json.return_value`
- Checks on mocks:
  - `assert_called_once_with(args)`
  - called N times (call count)
  - `assert_not_called()`
- Handy for routing-style code: check which mocks were / weren't called, and with what

### Mock a database

Code under test: `save_user()` uses `sqlite3.connect("users.db")`, gets a cursor, runs an `INSERT`.

```python
def test_save_user(mocker):
    mock_connect = mocker.patch("main.sqlite3.connect")
    mock_cursor = mock_connect.return_value.cursor.return_value

    save_user("Alice", 30)

    mock_connect.assert_called_once_with("users.db")
    mock_cursor.execute.assert_called_once_with("INSERT INTO users ...")
```

- Mock `connect`, then chain: `connection.return_value` → `.cursor.return_value`
- No real DB needed: test is fast and **no database file gets created**
- Checks the logic (right connection, right SQL), not SQLite itself

### Mock a whole class

Setup: `UserService` receives an `APIClient` and uses `api_client.get_user_data(id)`; it uppercases the username.

```python
def test_get_username(mocker):
    mock_client = mocker.Mock(spec=APIClient)
    mock_client.get_user_data.return_value = {"name": "Alice"}   # illustrative

    service = UserService(mock_client)      # inject the mock
    result = service.get_username(1)

    assert result == "ALICE"
    mock_client.get_user_data.assert_called_once_with(1)
```

- `mocker.Mock(spec=TheClass)` = fake instance of the class
- Then set return values on its methods
- Inject the mock instead of the real object → the service is tested without a working `APIClient`
- The service method itself is **not** mocked, only what it depends on



## Marks

Marks annotate tests to categorize them, skip them conditionally, or flag an expected failure.

### `skip` / `skipif`: ignore a test

```python
@pytest.mark.skip(reason="not implemented yet")
def test_future():
    ...

@pytest.mark.skipif(sys.version_info < (3, 11), reason="requires 3.11+")
def test_new_syntax():
    ...
```

### `xfail`: the test is expected to fail

- Use for a known bug or an incomplete feature
- Not counted as a failure
- Exception: if it unexpectedly passes, it is reported as `XPASS`

```python
@pytest.mark.xfail(reason="bug #123")
def test_known_broken():
    ...
```

### Custom marks

- Categorize tests and run only a subset

```python
@pytest.mark.slow
def test_long():
    ...
```

```
pytest -m slow          # only tests marked "slow"
pytest -m "not slow"    # exclude "slow" tests
```

- Custom marks must be declared in the configuration to avoid a warning

## Good practices

- Isolate tests: no dependencies between them, no shared side effects
- Keep tests fast; mark and isolate slow tests or ones needing external services
- One `conftest.py` per directory level, to share the fixtures relevant to that scope

## References:
- pytest documentationntation: [ https://docs.pytest.org/](https://docs.pytest.org/en/stable/getting-started.html)
- Pytest Tutorial: https://www.youtube.com/watch?v=EgpLj86ZHFQ
