# Week 4 — Automated Testing with Pytest

---

## Table of Contents

1. [Automated Testing Fundamentals](#1-automated-testing-fundamentals)
2. [Pytest Framework](#2-pytest-framework)
3. [Fixtures & Setup](#3-fixtures--setup)

---

## 1. Automated Testing Fundamentals

### Concept Overview

**Definition**
Automated testing is the practice of writing code whose sole purpose is to verify that your *other* code behaves correctly. Instead of a human manually running a program and checking the output, a test script does it — instantly, repeatedly, and identically every time.

**Why It Exists**
Imagine you build a calculator app. It works today. Next week you add a new feature. Did that change break addition? Without tests, you'd have to manually re-check every operation. With 10 features, that's tedious. With 1,000, it's impossible. Automated tests exist because:

- **Humans forget** — you won't remember to re-check every edge case.
- **Software grows** — every new line of code can break existing behavior.
- **Speed matters** — running 500 tests in 2 seconds beats 3 hours of manual clicking.
- **Confidence compounds** — tests let you refactor fearlessly.

**Mental Model**
Think of automated tests as a **safety net under a tightrope walker**. The walker (your code) can move fast and take risks because the net (tests) will catch any fall (bug). Without the net, every step is slow and terrifying.

```
Your Code (the tightrope)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
         🧑 ← You, adding features

 ╔══════════════════════════════════════╗
 ║        AUTOMATED TESTS              ║
 ║   (catches bugs before users do)    ║
 ╚══════════════════════════════════════╝
```

**Visual Explanation — Manual vs. Automated**

```
Manual Testing:
  Developer writes code
       ↓
  Developer runs program
       ↓
  Developer looks at output
       ↓
  Developer decides "looks right"    ← subjective, slow, unrepeatable
       ↓
  Ship it (and pray)

Automated Testing:
  Developer writes code + test code
       ↓
  Run: pytest                        ← one command
       ↓
  Computer checks every condition    ← objective, fast, repeatable
       ↓
  PASS → ship with confidence
  FAIL → fix before shipping
```

**When To Use It**
- Any code that will be maintained longer than a day.
- Any function with logic (conditions, loops, calculations).
- Before refactoring — tests prove you didn't break anything.
- In teams — tests are documentation that *runs*.

**When NOT To Use It**
- Throwaway scripts you'll delete in an hour.
- Testing third-party libraries (they should have their own tests).
- Testing Python built-ins like `len()` or `sorted()` — trust the language.

**Comparison With Similar Concepts**

| Approach | What It Checks | Speed | Reliability |
|---|---|---|---|
| **Manual testing** | Whatever you remember to check | Slow | Low (human error) |
| **Automated unit tests** | Individual functions/classes | Very fast | High |
| **Integration tests** | Multiple components together | Medium | High |
| **End-to-end tests** | Entire application flow | Slow | Highest (but brittle) |

This lesson focuses on **unit tests** — the foundation of all testing.

---

### Unit Tests

**Definition**
A **unit test** verifies a single, small piece of code (a function, a method, a class) in **isolation**. "Isolation" means the test doesn't depend on databases, networks, files, or other functions — just the one unit being tested.

**Three Properties of a Good Unit Test**

| Property | Meaning |
|---|---|
| **Fast** | Runs in milliseconds. You should be able to run hundreds per second. |
| **Isolated** | Tests one thing. If it fails, you know *exactly* what broke. |
| **Repeatable** | Same result every time, on any machine, in any order. |

**Mental Model**
Think of unit tests like **quality checks on an assembly line**. Each station checks one part. If the bolt station says "FAIL," you know the bolt is bad — you don't have to disassemble the entire car.

```
┌──────────┐    ┌──────────┐    ┌──────────┐
│ Test:    │    │ Test:    │    │ Test:    │
│ add()    │    │ subtract │    │ multiply │
│  ✅ PASS │    │  ✅ PASS │    │  ❌ FAIL │ ← Bug is HERE
└──────────┘    └──────────┘    └──────────┘
```

---

### Test-Driven Development (TDD)

**Definition**
**Test-Driven Development** is a discipline where you write the test *before* you write the code. It follows a strict three-step cycle:

```
┌─────────────────────────────────────────────────┐
│                                                 │
│   🔴 RED        →   🟢 GREEN    →   🔵 REFACTOR│
│   Write a test      Write just       Clean up   │
│   that FAILS        enough code      the code   │
│                     to PASS                      │
│         ↑                                ↓      │
│         └────────────────────────────────┘      │
│                  Repeat forever                  │
└─────────────────────────────────────────────────┘
```

**Step-by-step:**

1. **RED** — Write a test for behavior that doesn't exist yet. Run it. It *must* fail. (If it passes, your test is wrong.)
2. **GREEN** — Write the *minimum* code to make the test pass. No more, no less.
3. **REFACTOR** — Clean up your code (remove duplication, improve names) while keeping all tests green.

**Why This Order?**
- Writing the test first forces you to think about *what* the code should do before *how*.
- A failing test proves the test actually checks something real.
- Minimal code prevents over-engineering.

---

### Assertions

**Definition**
An **assertion** is a statement that declares "this condition must be true." If the condition is false, the program raises an `AssertionError`.

### 2️⃣ Syntax & Structure

**Canonical Syntax — `assert` Statement**

```python
assert condition, "optional error message"
```

**Line-by-Line Breakdown:**

- `assert` — Python keyword that evaluates the expression that follows.
- `condition` — Any expression that evaluates to `True` or `False`.
- `"optional error message"` — Displayed if the assertion fails. Helps you understand *what* went wrong.

**How Python Executes `assert` Internally:**

```
assert x == 5, "x should be 5"

# Python translates this to:
if not (x == 5):
    raise AssertionError("x should be 5")
```

**Important Warning:** Python can disable all `assert` statements with the `-O` (optimize) flag. Never use `assert` for input validation in production code — use `if/raise` instead. `assert` is for *testing* and *debugging* only.

### 3️⃣ Example 1 — Foundational

**Goal:** Write a simple function and a test that verifies it works.

```python
# math_utils.py

def add(a, b):
    """Return the sum of two numbers."""
    return a + b
```

```python
# test_math_utils.py

from math_utils import add    # Import the function we want to test

def test_add_positive_numbers():
    result = add(2, 3)        # Call the function with known inputs
    assert result == 5        # Verify the output matches expected value

def test_add_negative_numbers():
    result = add(-1, -1)
    assert result == -2

def test_add_zero():
    result = add(0, 0)
    assert result == 0
```

**Step-by-Step Runtime Walkthrough (for `test_add_positive_numbers`):**

1. Python imports `add` from `math_utils.py`.
2. The test runner (pytest) discovers `test_add_positive_numbers` because it starts with `test_`.
3. `add(2, 3)` is called → Python enters the `add` function → computes `2 + 3` → returns `5`.
4. `result` is now `5`.
5. `assert result == 5` → Python evaluates `5 == 5` → `True` → assertion passes silently.
6. Test is marked ✅ PASS.

**What Happens If It Fails?**

If `add` had a bug and returned `6`:

```
assert result == 5
       │
       ↓
    result = 6
    6 == 5 → False
       │
       ↓
    AssertionError raised
    Test marked ❌ FAIL
```

Pytest shows you *exactly* what went wrong:

```
    def test_add_positive_numbers():
        result = add(2, 3)
>       assert result == 5
E       assert 6 == 5
E        +  where 6 = add(2, 3)
```

This **introspection** is one of pytest's superpowers — it rewrites the `assert` to show you the actual vs. expected values without you writing any extra code.

**Runtime Visualization:**

```
test_add_positive_numbers()
│
├─ add(2, 3) called
│   └─ returns 5
│
├─ result = 5
│
├─ assert 5 == 5
│   └─ True → PASS ✅
│
└─ Test complete
```

**Output (when all tests pass):**

```
$ pytest test_math_utils.py
========================= test session starts =========================
collected 3 items

test_math_utils.py ...                                           [100%]

========================== 3 passed in 0.01s ==========================
```

The three dots `...` mean three tests, all passed.

### 4️⃣ Example 2 — Real-World / Interview Level

**Goal:** Demonstrate TDD by building a `FizzBuzz` function test-first. This is a classic interview problem: given a number, return `"Fizz"` if divisible by 3, `"Buzz"` if divisible by 5, `"FizzBuzz"` if divisible by both, or the number as a string otherwise.

**Step 1 — RED: Write failing tests first**

```python
# test_fizzbuzz.py

from fizzbuzz import fizzbuzz    # Module doesn't exist yet!

def test_regular_number():
    """Numbers not divisible by 3 or 5 return as string."""
    assert fizzbuzz(1) == "1"
    assert fizzbuzz(7) == "7"

def test_divisible_by_3():
    """Multiples of 3 return 'Fizz'."""
    assert fizzbuzz(3) == "Fizz"
    assert fizzbuzz(9) == "Fizz"

def test_divisible_by_5():
    """Multiples of 5 return 'Buzz'."""
    assert fizzbuzz(5) == "Buzz"
    assert fizzbuzz(20) == "Buzz"

def test_divisible_by_both():
    """Multiples of 15 return 'FizzBuzz'."""
    assert fizzbuzz(15) == "FizzBuzz"
    assert fizzbuzz(30) == "FizzBuzz"
```

Running `pytest` now → **RED** — `ModuleNotFoundError: No module named 'fizzbuzz'`.

**Step 2 — GREEN: Write minimum code to pass**

```python
# fizzbuzz.py

def fizzbuzz(n):
    """Convert number to FizzBuzz string."""
    if n % 15 == 0:       # Check 15 FIRST (both 3 and 5)
        return "FizzBuzz"
    elif n % 3 == 0:
        return "Fizz"
    elif n % 5 == 0:
        return "Buzz"
    else:
        return str(n)     # Convert int to string
```

**Deep Explanation:**

- **Line `if n % 15 == 0`** — The `%` operator returns the remainder of division. `15 % 15 = 0`, so this catches numbers divisible by both 3 and 5. We check 15 *first* because if we checked 3 first, `15` would match `Fizz` and never reach `FizzBuzz`.
- **Line `return str(n)`** — The tests expect a string (`"1"`, not `1`), so we convert with `str()`.

**Why check 15 before 3?**

```
Order matters! If we check 3 first:

  fizzbuzz(15)
  │
  ├─ 15 % 3 == 0?  → YES → return "Fizz"  ← WRONG!
  │                         (never reaches FizzBuzz check)

Correct order (check 15 first):

  fizzbuzz(15)
  │
  ├─ 15 % 15 == 0? → YES → return "FizzBuzz"  ← CORRECT!
```

**Step 3 — REFACTOR: Code is already clean, but let's verify**

Running `pytest`:

```
$ pytest test_fizzbuzz.py -v
========================= test session starts =========================
collected 4 items

test_fizzbuzz.py::test_regular_number      PASSED                [ 25%]
test_fizzbuzz.py::test_divisible_by_3      PASSED                [ 50%]
test_fizzbuzz.py::test_divisible_by_5      PASSED                [ 75%]
test_fizzbuzz.py::test_divisible_by_both   PASSED                [100%]

========================== 4 passed in 0.01s ==========================
```

The `-v` flag gives **verbose** output — showing each test name and result individually.

**Runtime Flow Diagram (for `test_divisible_by_both`):**

```
test_divisible_by_both()
│
├─ fizzbuzz(15) called
│   ├─ 15 % 15 == 0? → True
│   └─ return "FizzBuzz"
│
├─ assert "FizzBuzz" == "FizzBuzz" → True ✅
│
├─ fizzbuzz(30) called
│   ├─ 30 % 15 == 0? → True
│   └─ return "FizzBuzz"
│
├─ assert "FizzBuzz" == "FizzBuzz" → True ✅
│
└─ Test PASSED ✅
```

---

## 2. Pytest Framework

### Concept Overview

**Definition**
**pytest** is a third-party Python testing framework that makes writing and running tests simple. It's the most popular testing tool in the Python ecosystem, used by projects like Django, Flask, and even parts of CPython itself.

**Why It Exists**
Python has a built-in testing module called `unittest`, but it requires:
- Classes for every test group
- `self.assertEqual()`, `self.assertTrue()`, etc. — verbose assertion methods
- Boilerplate setup

pytest strips all that away. You write plain functions with plain `assert` statements, and pytest handles the rest.

**Mental Model**
Think of pytest as a **smart detective**. You give it a folder of code. It:
1. **Finds** all test files automatically (no configuration needed).
2. **Runs** every test function it discovers.
3. **Reports** exactly what passed, what failed, and *why*.

```
Your Project Folder
├── src/
│   ├── calculator.py
│   └── validator.py
└── tests/
    ├── test_calculator.py     ← pytest finds this (starts with test_)
    ├── test_validator.py      ← pytest finds this too
    └── helpers.py             ← pytest IGNORES this (no test_ prefix)

$ pytest
→ Scans folders
→ Finds test_calculator.py, test_validator.py
→ Finds functions starting with test_
→ Runs them all
→ Reports results
```

**Comparison: pytest vs. unittest**

| Feature | `unittest` (built-in) | `pytest` |
|---|---|---|
| Test structure | Must use classes | Plain functions |
| Assertions | `self.assertEqual(a, b)` | `assert a == b` |
| Output on failure | Generic message | Shows actual values |
| Fixtures | `setUp()`/`tearDown()` methods | `@pytest.fixture` decorator |
| Plugins | Limited | 800+ community plugins |
| Learning curve | Steeper | Gentler |

**When To Use It**
- Every Python project that has tests (which should be every project).
- When you want clear, readable tests with minimal boilerplate.

**When NOT To Use It**
- If your project *requires* `unittest` style (some legacy codebases).
- pytest can still run `unittest`-style tests, so even then, you can use pytest as the *runner*.

---

### Installation

### 2️⃣ Syntax & Structure

**Installing pytest as a development dependency:**

```bash
uv add --dev pytest
```

**Line-by-Line Breakdown:**

- `uv` — The modern Python package manager (covered in Week 3).
- `add` — Subcommand to add a dependency.
- `--dev` — Marks this as a **development dependency**. This means pytest is needed for development/testing but NOT shipped to end users. When someone installs your library, they don't get pytest.
- `pytest` — The package name.

**What This Does Internally:**

```
uv add --dev pytest
│
├─ Resolves latest compatible pytest version
├─ Downloads pytest + its dependencies
├─ Installs into virtual environment (.venv/)
├─ Updates pyproject.toml:
│   [dependency-groups]
│   dev = ["pytest>=8.0"]
│
└─ Updates uv.lock (exact pinned versions)
```

---

### Execution

**Running tests:**

```bash
# Option 1: Through uv (always works)
uv run pytest

# Option 2: Direct (if virtual environment is activated)
pytest

# Option 3: Run specific file
pytest tests/test_calculator.py

# Option 4: Run specific test function
pytest tests/test_calculator.py::test_add_positive

# Option 5: Verbose output (shows each test name)
pytest -v

# Option 6: Stop on first failure
pytest -x
```

**Breakdown of Options:**

- `uv run pytest` — `uv run` ensures the virtual environment is active, then runs `pytest` inside it. Safest option.
- `pytest -v` — Verbose mode. Instead of dots, shows full test names and PASSED/FAILED.
- `pytest -x` — Exit on first failure. Useful when debugging — fix one thing at a time.

---

### Discovery Rules

**How pytest finds your tests — the three rules:**

```
Rule 1: FILE names must match test_*.py or *_test.py
Rule 2: FUNCTION names must start with test_
Rule 3: CLASS names must start with Test (and have no __init__ method)
```

**Visual Discovery Process:**

```
$ pytest

Step 1 — Scan directories (starting from current folder)
    ├── app.py                    ← SKIP (not test_*.py)
    ├── test_app.py               ← MATCH ✅
    ├── app_test.py               ← MATCH ✅
    ├── tests/
    │   ├── test_models.py        ← MATCH ✅
    │   └── conftest.py           ← SPECIAL (fixtures, not tests)
    └── utils/
        └── helpers.py            ← SKIP

Step 2 — Inside each matched file, find test items
    test_app.py:
        def test_homepage():      ← MATCH ✅ (starts with test_)
        def helper_func():        ← SKIP (no test_ prefix)
        class TestLogin:          ← MATCH ✅ (starts with Test)
            def test_valid():     ← MATCH ✅
            def test_invalid():   ← MATCH ✅
        class MyTests:            ← SKIP (doesn't start with Test)
```

### 3️⃣ Example 1 — Foundational

**Goal:** Set up a project with pytest and write tests that demonstrate discovery rules.

**Project Structure:**

```
my_project/
├── pyproject.toml
├── src/
│   └── calculator.py
└── tests/
    ├── test_calculator.py
    └── test_edge_cases.py
```

```python
# src/calculator.py

def divide(a, b):
    """Divide a by b. Raises ValueError if b is zero."""
    if b == 0:
        raise ValueError("Cannot divide by zero")
    return a / b
```

```python
# tests/test_calculator.py

from src.calculator import divide

def test_divide_integers():
    """Test basic integer division."""
    result = divide(10, 2)
    assert result == 5.0          # Division always returns float

def test_divide_returns_float():
    """Verify return type is always float."""
    result = divide(9, 3)
    assert isinstance(result, float)  # isinstance checks the type

def test_divide_negative():
    """Test division with negative numbers."""
    assert divide(-10, 2) == -5.0
    assert divide(10, -2) == -5.0
```

**Step-by-Step Runtime Walkthrough:**

1. You run `pytest` from the `my_project/` directory.
2. pytest scans all subdirectories.
3. It finds `tests/test_calculator.py` — filename matches `test_*.py`.
4. Inside, it finds three functions starting with `test_`.
5. It runs `test_divide_integers()`:
   - Calls `divide(10, 2)`.
   - Inside `divide`: `b` is `2`, not `0`, so the `if` is skipped.
   - `10 / 2` evaluates to `5.0` (Python's `/` always returns a float).
   - `result = 5.0`.
   - `assert 5.0 == 5.0` → `True` → PASS.
6. Repeats for the other two tests.

**Output:**

```
$ pytest tests/test_calculator.py -v
========================= test session starts =========================
collected 3 items

tests/test_calculator.py::test_divide_integers     PASSED        [ 33%]
tests/test_calculator.py::test_divide_returns_float PASSED        [ 66%]
tests/test_calculator.py::test_divide_negative      PASSED        [100%]

========================== 3 passed in 0.02s ==========================
```

---

### Handling Exceptions

**Definition**
Sometimes the *correct* behavior of a function is to raise an exception. You need to test that the exception actually gets raised. pytest provides `pytest.raises()` as a **context manager** for this.

### 2️⃣ Syntax & Structure

```python
import pytest

def test_something_raises():
    with pytest.raises(ExpectedErrorType):
        code_that_should_raise()
```

**Line-by-Line Breakdown:**

- `import pytest` — Import the pytest module to access `pytest.raises`.
- `with pytest.raises(ExpectedErrorType):` — This is a **context manager** (the `with` block). It tells pytest: "I expect the code inside this block to raise `ExpectedErrorType`. If it does, the test passes. If it doesn't raise, or raises a *different* error, the test fails."
- `code_that_should_raise()` — The actual function call you're testing.

**How It Works Internally:**

```
with pytest.raises(ValueError):
    divide(10, 0)

Execution:
1. pytest.raises(ValueError) sets up a "trap" for ValueError
2. divide(10, 0) runs
3. Inside divide: b == 0 → raise ValueError("Cannot divide by zero")
4. ValueError is raised
5. The "trap" catches it
6. ValueError matches expected ValueError → TEST PASSES ✅

What if divide(10, 0) did NOT raise?
→ pytest.raises detects no exception was raised
→ TEST FAILS with: "DID NOT RAISE ValueError"
```

### 3️⃣ Example — Testing Exceptions

```python
# tests/test_calculator.py (continued)

import pytest
from src.calculator import divide

def test_divide_by_zero_raises():
    """Verify that dividing by zero raises ValueError."""
    with pytest.raises(ValueError):
        divide(10, 0)

def test_divide_by_zero_message():
    """Verify the error message is correct."""
    with pytest.raises(ValueError) as exc_info:    # Capture exception details
        divide(10, 0)
    assert str(exc_info.value) == "Cannot divide by zero"
```

**Deep Explanation of `test_divide_by_zero_message`:**

- `as exc_info` — Captures the exception information into a variable.
- `exc_info.value` — The actual exception object that was raised.
- `str(exc_info.value)` — Converts the exception to its string message.
- We then assert the message matches exactly.

**Runtime Visualization:**

```
test_divide_by_zero_message()
│
├─ Enter pytest.raises(ValueError) context
│   └─ Trap set for ValueError
│
├─ divide(10, 0) called
│   ├─ b == 0? → True
│   └─ raise ValueError("Cannot divide by zero")
│
├─ ValueError caught by trap
│   └─ exc_info.value = ValueError("Cannot divide by zero")
│
├─ str(exc_info.value) → "Cannot divide by zero"
├─ assert "Cannot divide by zero" == "Cannot divide by zero" → True ✅
│
└─ Test PASSED ✅
```

### 4️⃣ Example 2 — Real-World / Interview Level

**Goal:** Build and test a `UserValidator` class using TDD, demonstrating exception testing, multiple assertions, and realistic validation logic.

```python
# src/validator.py

class UserValidator:
    """Validates user registration data."""

    FORBIDDEN_USERNAMES = {"admin", "root", "system", "null"}

    def validate_username(self, username):
        """
        Validate a username string.

        Rules:
        - Must be a string
        - Must be 3-20 characters long
        - Must contain only alphanumeric characters and underscores
        - Must not be a forbidden name
        """
        if not isinstance(username, str):
            raise TypeError("Username must be a string")

        if len(username) < 3 or len(username) > 20:
            raise ValueError(
                f"Username must be 3-20 characters, got {len(username)}"
            )

        if not username.replace("_", "").isalnum():
            raise ValueError(
                "Username must contain only letters, numbers, and underscores"
            )

        if username.lower() in self.FORBIDDEN_USERNAMES:
            raise ValueError(f"Username '{username}' is forbidden")

        return True    # Explicitly return True for valid usernames
```

```python
# tests/test_validator.py

import pytest
from src.validator import UserValidator

# --- Tests for VALID usernames ---

def test_valid_simple_username():
    """Standard alphanumeric username should pass."""
    validator = UserValidator()
    assert validator.validate_username("alice") is True

def test_valid_username_with_underscore():
    """Underscores are allowed in usernames."""
    validator = UserValidator()
    assert validator.validate_username("alice_99") is True

def test_valid_minimum_length():
    """Exactly 3 characters should pass (boundary test)."""
    validator = UserValidator()
    assert validator.validate_username("abc") is True

def test_valid_maximum_length():
    """Exactly 20 characters should pass (boundary test)."""
    validator = UserValidator()
    assert validator.validate_username("a" * 20) is True

# --- Tests for INVALID usernames ---

def test_too_short():
    """Username under 3 characters should raise ValueError."""
    validator = UserValidator()
    with pytest.raises(ValueError) as exc_info:
        validator.validate_username("ab")
    assert "3-20 characters" in str(exc_info.value)

def test_too_long():
    """Username over 20 characters should raise ValueError."""
    validator = UserValidator()
    with pytest.raises(ValueError):
        validator.validate_username("a" * 21)

def test_wrong_type():
    """Non-string input should raise TypeError."""
    validator = UserValidator()
    with pytest.raises(TypeError):
        validator.validate_username(12345)

def test_special_characters():
    """Special characters should raise ValueError."""
    validator = UserValidator()
    with pytest.raises(ValueError):
        validator.validate_username("alice@home")

def test_forbidden_username():
    """Forbidden names should raise ValueError regardless of case."""
    validator = UserValidator()
    with pytest.raises(ValueError) as exc_info:
        validator.validate_username("Admin")    # Mixed case
    assert "forbidden" in str(exc_info.value)
```

**Deep Explanation — Key Testing Patterns Used:**

1. **Boundary Testing** (`test_valid_minimum_length`, `test_valid_maximum_length`): Tests the exact edges of valid ranges. Bugs love boundaries — off-by-one errors are the most common bug in programming.

2. **Error Message Verification** (`test_too_short`, `test_forbidden_username`): Not just checking that an error is raised, but that the message is helpful. This matters because error messages are part of your API.

3. **Type Checking** (`test_wrong_type`): Verifying that wrong input types are caught early with the correct exception type (`TypeError`, not `ValueError`).

4. **Case Sensitivity** (`test_forbidden_username`): Testing `"Admin"` (mixed case) against the forbidden list ensures the `.lower()` conversion works.

**Runtime Flow Diagram (for `test_forbidden_username`):**

```
test_forbidden_username()
│
├─ validator = UserValidator()
│   └─ FORBIDDEN_USERNAMES = {"admin", "root", "system", "null"}
│
├─ Enter pytest.raises(ValueError) context
│
├─ validator.validate_username("Admin") called
│   ├─ isinstance("Admin", str)? → True (pass)
│   ├─ len("Admin") = 5, 3 ≤ 5 ≤ 20? → True (pass)
│   ├─ "Admin".replace("_","") = "Admin"
│   │   "Admin".isalnum()? → True (pass)
│   ├─ "Admin".lower() = "admin"
│   │   "admin" in {"admin","root","system","null"}? → True
│   └─ raise ValueError("Username 'Admin' is forbidden")
│
├─ ValueError caught by pytest.raises
│   └─ exc_info.value = ValueError("Username 'Admin' is forbidden")
│
├─ "forbidden" in "Username 'Admin' is forbidden"? → True ✅
│
└─ Test PASSED ✅
```

**Output:**

```
$ pytest tests/test_validator.py -v
========================= test session starts =========================
collected 9 items

tests/test_validator.py::test_valid_simple_username       PASSED  [ 11%]
tests/test_validator.py::test_valid_username_with_underscore PASSED [ 22%]
tests/test_validator.py::test_valid_minimum_length        PASSED  [ 33%]
tests/test_validator.py::test_valid_maximum_length        PASSED  [ 44%]
tests/test_validator.py::test_too_short                   PASSED  [ 55%]
tests/test_validator.py::test_too_long                    PASSED  [ 66%]
tests/test_validator.py::test_wrong_type                  PASSED  [ 77%]
tests/test_validator.py::test_special_characters          PASSED  [ 88%]
tests/test_validator.py::test_forbidden_username          PASSED  [100%]

========================== 9 passed in 0.03s ==========================
```

---

## 3. Fixtures & Setup

### Concept Overview

**Definition**
A **fixture** is a function that provides **setup** (and optionally **teardown**) code for your tests. Instead of repeating the same preparation in every test, you define it once as a fixture and pytest automatically injects it where needed.

**Why It Exists**
Look at the validator tests above. Every single test starts with:

```python
validator = UserValidator()
```

That's repeated 9 times. Now imagine your setup requires creating a database connection, loading test data, and configuring settings — repeating that in 50 tests is a maintenance nightmare. Fixtures solve this by centralizing setup code.

**Mental Model**
Think of fixtures as a **prep kitchen in a restaurant**. Before any dish (test) is made, the prep kitchen (fixture) has already:
- Washed the vegetables (created objects)
- Heated the oven (set up connections)
- Laid out the tools (prepared test data)

Each chef (test function) just walks in and everything is ready.

```
Without Fixtures:                    With Fixtures:

test_1():                            @pytest.fixture
  setup A                            def prepared_data():
  setup B                                setup A
  setup C                                setup B
  actual test                            setup C
                                         return data
test_2():
  setup A    ← repeated!            test_1(prepared_data):
  setup B    ← repeated!                actual test    ← clean!
  setup C    ← repeated!
  actual test                        test_2(prepared_data):
                                         actual test    ← clean!
```

**When To Use It**
- When multiple tests need the same setup.
- When setup is complex (database connections, file creation, API clients).
- When you need cleanup after tests (closing connections, deleting temp files).

**When NOT To Use It**
- When setup is a single, simple line — just write it in the test.
- When each test needs *different* setup — fixtures add unnecessary abstraction.

---

### Implementation

### 2️⃣ Syntax & Structure

```python
import pytest

@pytest.fixture
def my_fixture():
    # Setup code
    data = create_something()
    return data                  # This value is passed to the test

def test_something(my_fixture):  # pytest sees the parameter name
    assert my_fixture.is_valid() # and injects the fixture's return value
```

**Line-by-Line Breakdown:**

- `@pytest.fixture` — Decorator that tells pytest "this function is a fixture, not a test."
- `def my_fixture():` — The fixture function. Its **name** is how tests request it.
- `return data` — The value returned here is what the test receives.
- `def test_something(my_fixture):` — The parameter name `my_fixture` **must match** the fixture function name exactly. pytest uses this name to look up and inject the fixture.

**How Pytest Injection Works:**

```
When pytest sees:
    def test_something(my_fixture):

It does:
1. "test_something needs a parameter called 'my_fixture'"
2. "Is there a fixture named 'my_fixture'?"  → Yes!
3. Call my_fixture() → get return value
4. Pass return value as argument to test_something()
```

This is called **dependency injection** — the test declares what it needs, and the framework provides it.

---

### Fixtures with Teardown (yield)

```python
@pytest.fixture
def database_connection():
    # SETUP — runs before the test
    conn = create_connection("test.db")

    yield conn    # Provide the connection to the test

    # TEARDOWN — runs after the test (even if test fails!)
    conn.close()
    delete_database("test.db")
```

**Key Difference: `return` vs. `yield`**

| | `return` | `yield` |
|---|---|---|
| Setup | ✅ | ✅ |
| Provides value to test | ✅ | ✅ |
| Teardown after test | ❌ | ✅ (code after `yield`) |

**Execution Flow:**

```
test_query(database_connection):
│
├─ BEFORE test: fixture runs up to yield
│   └─ conn = create_connection("test.db")
│
├─ yield conn → conn is passed to test
│
├─ TEST RUNS: test_query uses conn
│
└─ AFTER test: fixture resumes after yield
    ├─ conn.close()
    └─ delete_database("test.db")
```

---

### Scopes

**Definition**
A fixture's **scope** controls how often it runs. By default, a fixture runs once per test function. But you can change this.

```python
@pytest.fixture(scope="function")   # Default: once per test
def per_test_data():
    return create_data()

@pytest.fixture(scope="module")     # Once per file
def per_file_connection():
    return create_connection()

@pytest.fixture(scope="session")    # Once for ALL tests
def per_suite_config():
    return load_config()
```

**Visual Comparison:**

```
Test File: test_users.py (3 tests)
Test File: test_orders.py (2 tests)

scope="function" (default):
  test_users.py:
    test_1 → fixture runs ①
    test_2 → fixture runs ②
    test_3 → fixture runs ③
  test_orders.py:
    test_4 → fixture runs ④
    test_5 → fixture runs ⑤
  Total fixture calls: 5

scope="module":
  test_users.py:
    fixture runs ① (once for this file)
    test_1 → uses ①
    test_2 → uses ①
    test_3 → uses ①
  test_orders.py:
    fixture runs ② (once for this file)
    test_4 → uses ②
    test_5 → uses ②
  Total fixture calls: 2

scope="session":
  fixture runs ① (once for everything)
  test_users.py:
    test_1 → uses ①
    test_2 → uses ①
    test_3 → uses ①
  test_orders.py:
    test_4 → uses ①
    test_5 → uses ①
  Total fixture calls: 1
```

**When To Use Each Scope:**

| Scope | Use When | Example |
|---|---|---|
| `function` | Each test needs fresh, isolated data | Creating a new user object |
| `module` | Setup is expensive but tests in a file can share it | Database connection per file |
| `session` | Setup is very expensive and all tests can share it | Loading a large config file |

**Warning:** Higher scopes (module, session) mean tests share state. If one test modifies the shared fixture, it can affect other tests. Use higher scopes only for **read-only** or **stateless** fixtures.

---

### Built-in Fixtures

pytest provides several fixtures out of the box. The two most important:

#### `tmp_path` — Temporary Directory

```python
def test_write_file(tmp_path):
    """tmp_path gives a unique temporary directory for each test."""
    file = tmp_path / "output.txt"     # Create a Path object
    file.write_text("hello world")     # Write to the file
    assert file.read_text() == "hello world"
    # tmp_path is automatically cleaned up after the test
```

**Explanation:**

- `tmp_path` is a `pathlib.Path` object pointing to a unique temporary directory.
- Each test gets its own directory — no conflicts between tests.
- The `/` operator on `Path` objects joins paths (like `os.path.join` but cleaner).
- After the test, the directory and its contents are cleaned up automatically.

**Why It Matters:**
If your code reads/writes files, you need to test that. But you don't want tests creating files in your project directory. `tmp_path` gives you a safe, isolated sandbox.

#### `capsys` — Capture Print Output

```python
def test_greeting(capsys):
    """capsys captures anything printed to stdout."""
    print("Hello, World!")
    captured = capsys.readouterr()     # Read captured output
    assert captured.out == "Hello, World!\n"   # Note: print adds \n
```

**Explanation:**

- `capsys` intercepts everything written to `sys.stdout` (what `print()` uses).
- `capsys.readouterr()` returns a named tuple with `.out` (stdout) and `.err` (stderr).
- The `\n` at the end is because `print()` adds a newline by default.

**Why It Matters:**
Many functions communicate through `print()`. Without `capsys`, you'd have no way to verify what was printed.

---

### Sharing Fixtures with `conftest.py`

**Definition**
`conftest.py` is a special file that pytest automatically discovers. Any fixture defined in `conftest.py` is available to all test files in the same directory (and subdirectories) **without importing it**.

```
tests/
├── conftest.py          ← Fixtures here are available to ALL test files
├── test_users.py        ← Can use fixtures from conftest.py
├── test_orders.py       ← Can use fixtures from conftest.py
└── integration/
    ├── conftest.py      ← Additional fixtures for this subdirectory
    └── test_api.py      ← Can use fixtures from BOTH conftest.py files
```

**Why No Import Is Needed:**
pytest has a plugin system. When it starts, it scans for all `conftest.py` files and registers their fixtures globally (within their scope). This is **magic by design** — it keeps test files clean.

### 3️⃣ Example 1 — Foundational

**Goal:** Refactor the validator tests to use a fixture, eliminating repeated setup.

```python
# tests/conftest.py

import pytest
from src.validator import UserValidator

@pytest.fixture
def validator():
    """Provide a fresh UserValidator instance for each test."""
    return UserValidator()
```

```python
# tests/test_validator.py (refactored)

import pytest

# Notice: NO import of UserValidator or the fixture!
# pytest finds the fixture from conftest.py automatically.

def test_valid_username(validator):          # ← fixture injected
    assert validator.validate_username("alice") is True

def test_too_short(validator):              # ← fixture injected
    with pytest.raises(ValueError):
        validator.validate_username("ab")

def test_wrong_type(validator):             # ← fixture injected
    with pytest.raises(TypeError):
        validator.validate_username(12345)

def test_forbidden_name(validator):         # ← fixture injected
    with pytest.raises(ValueError):
        validator.validate_username("admin")
```

**Step-by-Step Runtime Walkthrough (for `test_valid_username`):**

1. pytest discovers `test_validator.py`.
2. It finds `test_valid_username(validator)`.
3. "This test needs a parameter called `validator`."
4. pytest searches for a fixture named `validator`:
   - Check `test_validator.py` — not found.
   - Check `conftest.py` — **found!**
5. pytest calls the `validator()` fixture → returns a new `UserValidator()` instance.
6. pytest passes that instance to `test_valid_username` as the `validator` argument.
7. The test runs: `validator.validate_username("alice")` → returns `True`.
8. `assert True is True` → PASS.
9. The fixture's return value is discarded (scope is `function`, so a fresh one is created for the next test).

**Runtime Visualization:**

```
pytest starts
│
├─ Discovers conftest.py
│   └─ Registers fixture: "validator"
│
├─ Discovers test_validator.py
│   └─ Found 4 test functions
│
├─ test_valid_username(validator)
│   ├─ Need fixture "validator" → call validator()
│   │   └─ return UserValidator()  [instance #1]
│   ├─ test runs with instance #1
│   └─ PASS ✅ → instance #1 discarded
│
├─ test_too_short(validator)
│   ├─ Need fixture "validator" → call validator()
│   │   └─ return UserValidator()  [instance #2]  ← FRESH instance
│   ├─ test runs with instance #2
│   └─ PASS ✅ → instance #2 discarded
│
└─ ... (same pattern for remaining tests)
```

### 4️⃣ Example 2 — Real-World / Interview Level

**Goal:** Build a test suite for a file-based note-taking system, using `tmp_path`, `capsys`, `yield` fixtures, and `conftest.py` together.

```python
# src/notes.py

from pathlib import Path

class NoteBook:
    """A simple file-based notebook."""

    def __init__(self, directory):
        """Initialize with a directory to store notes."""
        self.directory = Path(directory)
        self.directory.mkdir(parents=True, exist_ok=True)

    def add_note(self, title, content):
        """Save a note as a text file."""
        filepath = self.directory / f"{title}.txt"
        if filepath.exists():
            raise FileExistsError(f"Note '{title}' already exists")
        filepath.write_text(content)
        print(f"Note '{title}' saved.")
        return filepath

    def read_note(self, title):
        """Read a note by title."""
        filepath = self.directory / f"{title}.txt"
        if not filepath.exists():
            raise FileNotFoundError(f"Note '{title}' not found")
        return filepath.read_text()

    def list_notes(self):
        """Return sorted list of note titles."""
        return sorted(
            f.stem for f in self.directory.glob("*.txt")
        )

    def delete_note(self, title):
        """Delete a note by title."""
        filepath = self.directory / f"{title}.txt"
        if not filepath.exists():
            raise FileNotFoundError(f"Note '{title}' not found")
        filepath.unlink()     # Delete the file
        print(f"Note '{title}' deleted.")
```

```python
# tests/conftest.py

import pytest
from src.notes import NoteBook

@pytest.fixture
def notebook(tmp_path):
    """
    Provide a NoteBook that uses a temporary directory.

    This fixture DEPENDS on the built-in tmp_path fixture.
    pytest handles the chain: tmp_path → notebook → test
    """
    return NoteBook(tmp_path / "test_notes")

@pytest.fixture
def populated_notebook(notebook):
    """
    Provide a notebook pre-loaded with sample notes.

    This fixture DEPENDS on the notebook fixture above.
    Fixture chaining: tmp_path → notebook → populated_notebook → test
    """
    notebook.add_note("shopping", "Buy milk and eggs")
    notebook.add_note("todo", "Finish homework")
    notebook.add_note("ideas", "Learn pytest fixtures")
    return notebook
```

```python
# tests/test_notes.py

import pytest

def test_add_note(notebook, capsys):
    """Test creating a new note."""
    result = notebook.add_note("greeting", "Hello World")

    # Verify file was created
    assert result.exists()
    assert result.name == "greeting.txt"

    # Verify content
    assert result.read_text() == "Hello World"

    # Verify print output
    captured = capsys.readouterr()
    assert "Note 'greeting' saved." in captured.out

def test_add_duplicate_raises(notebook):
    """Adding a note with existing title should raise."""
    notebook.add_note("test", "first")
    with pytest.raises(FileExistsError):
        notebook.add_note("test", "second")    # Same title!

def test_read_note(populated_notebook):
    """Test reading an existing note."""
    content = populated_notebook.read_note("shopping")
    assert content == "Buy milk and eggs"

def test_read_missing_raises(notebook):
    """Reading non-existent note should raise."""
    with pytest.raises(FileNotFoundError):
        notebook.read_note("nonexistent")

def test_list_notes(populated_notebook):
    """Test listing all notes (should be sorted)."""
    titles = populated_notebook.list_notes()
    assert titles == ["ideas", "shopping", "todo"]    # Alphabetical

def test_delete_note(populated_notebook, capsys):
    """Test deleting a note."""
    populated_notebook.delete_note("todo")

    # Verify it's gone
    assert "todo" not in populated_notebook.list_notes()

    # Verify print output
    captured = capsys.readouterr()
    assert "Note 'todo' deleted." in captured.out

def test_delete_missing_raises(notebook):
    """Deleting non-existent note should raise."""
    with pytest.raises(FileNotFoundError):
        notebook.delete_note("ghost")
```

**Deep Explanation — Fixture Chaining:**

The most powerful concept here is **fixture chaining**. Look at how `populated_notebook` works:

```
pytest needs to run: test_list_notes(populated_notebook)

Step 1: "test needs populated_notebook fixture"
Step 2: "populated_notebook needs notebook fixture"
Step 3: "notebook needs tmp_path fixture"
Step 4: Build the chain bottom-up:

    tmp_path (built-in)
        │
        ↓ provides temporary directory
    notebook(tmp_path)
        │
        ↓ provides empty NoteBook
    populated_notebook(notebook)
        │
        ↓ adds 3 sample notes, returns NoteBook
    test_list_notes(populated_notebook)
        │
        ↓ runs the actual test
```

**Runtime Flow Diagram (for `test_delete_note`):**

```
test_delete_note(populated_notebook, capsys)
│
├─ FIXTURE CHAIN:
│   ├─ tmp_path creates /tmp/pytest-xyz/test_delete_note0/
│   ├─ notebook creates NoteBook at .../test_notes/
│   └─ populated_notebook adds 3 notes:
│       ├─ shopping.txt → "Buy milk and eggs"
│       ├─ todo.txt     → "Finish homework"
│       └─ ideas.txt    → "Learn pytest fixtures"
│
├─ capsys starts capturing stdout
│
├─ populated_notebook.delete_note("todo")
│   ├─ filepath = .../test_notes/todo.txt
│   ├─ filepath.exists()? → True
│   ├─ filepath.unlink() → file deleted from disk
│   └─ print("Note 'todo' deleted.")
│
├─ populated_notebook.list_notes()
│   └─ glob("*.txt") finds: ideas.txt, shopping.txt
│       └─ returns ["ideas", "shopping"]
│
├─ "todo" not in ["ideas", "shopping"]? → True ✅
│
├─ capsys.readouterr()
│   └─ captured.out = "Note 'todo' deleted.\n"
│
├─ "Note 'todo' deleted." in captured.out? → True ✅
│
└─ Test PASSED ✅
│
└─ CLEANUP: tmp_path directory deleted automatically
```

**Output:**

```
$ pytest tests/test_notes.py -v
========================= test session starts =========================
collected 7 items

tests/test_notes.py::test_add_note              PASSED           [ 14%]
tests/test_notes.py::test_add_duplicate_raises   PASSED           [ 28%]
tests/test_notes.py::test_read_note              PASSED           [ 42%]
tests/test_notes.py::test_read_missing_raises    PASSED           [ 57%]
tests/test_notes.py::test_list_notes             PASSED           [ 71%]
tests/test_notes.py::test_delete_note            PASSED           [ 85%]
tests/test_notes.py::test_delete_missing_raises  PASSED           [100%]

========================== 7 passed in 0.05s ==========================
```

---

## Quick Reference Card

```
TESTING CHEAT SHEET
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Install:        uv add --dev pytest
Run all:        uv run pytest
Run verbose:    pytest -v
Run one file:   pytest tests/test_file.py
Run one test:   pytest tests/test_file.py::test_name
Stop on fail:   pytest -x

ASSERTIONS:
  assert x == y              # equality
  assert x is True           # identity
  assert x in collection     # membership
  assert isinstance(x, int)  # type check

EXCEPTIONS:
  with pytest.raises(ValueError):
      risky_function()

FIXTURES:
  @pytest.fixture
  def my_data():
      return {"key": "value"}

  def test_something(my_data):    # name must match!
      assert my_data["key"] == "value"

FIXTURE SCOPES:
  @pytest.fixture(scope="function")   # per test (default)
  @pytest.fixture(scope="module")     # per file
  @pytest.fixture(scope="session")    # per suite

BUILT-IN FIXTURES:
  tmp_path    → temporary directory (Path object)
  capsys      → capture print output

FIXTURE TEARDOWN:
  @pytest.fixture
  def resource():
      conn = setup()
      yield conn        # test runs here
      conn.close()      # teardown runs after

SHARING:
  conftest.py → fixtures available to all tests
                in same directory (no import needed)

DISCOVERY RULES:
  Files:     test_*.py or *_test.py
  Functions: test_*
  Classes:   Test*
```
