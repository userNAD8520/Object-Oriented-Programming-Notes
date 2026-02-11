# Week 3 — Advanced Functions, Decorators & Modern Project Management

---

## Table of Contents

1. [Advanced Function Arguments](#1-advanced-function-arguments)
   - [Concept Overview](#concept-overview)
   - [Syntax & Structure](#2️⃣-syntax--structure)
   - [Example 1 — Foundational: *args and **kwargs](#3️⃣-example-1--foundational-args-and-kwargs)
   - [Example 2 — Real-World: Unpacking & Flexible APIs](#4️⃣-example-2--real-world-unpacking--flexible-apis)
2. [Higher-Order Functions & Decorators](#2-higher-order-functions--decorators)
   - [Concept Overview](#concept-overview-1)
   - [Syntax & Structure](#2️⃣-syntax--structure-1)
   - [Example 1 — Foundational: Closures & First Decorators](#3️⃣-example-1--foundational-closures--first-decorators)
   - [Example 2 — Real-World: Logging, Timing, Caching & Stacking](#4️⃣-example-2--real-world-logging-timing-caching--stacking)
3. [Modern Project Management & Environment](#3-modern-project-management--environment)
   - [Concept Overview](#concept-overview-2)
   - [Syntax & Structure](#2️⃣-syntax--structure-2)
   - [Example 1 — Foundational: Virtual Environments & uv](#3️⃣-example-1--foundational-virtual-environments--uv)
   - [Example 2 — Real-World: Secrets, .env, and VS Code Debugging](#4️⃣-example-2--real-world-secrets-env-and-vs-code-debugging)

---

# 1. Advanced Function Arguments

---

## Concept Overview

### Definition

Python functions can accept arguments in several flexible ways beyond simple positional parameters:

- **`*args`** — Collects any number of extra **positional** arguments into a **tuple**.
- **`**kwargs`** — Collects any number of extra **keyword** arguments into a **dictionary**.
- **Unpacking (`*` and `**`)** — The reverse: spreads a list/tuple or dictionary into individual arguments when *calling* a function.

### Why It Exists

Real-world functions often need flexibility:

- A `print()` function that accepts any number of values.
- A `dict.update()` that accepts any number of key-value pairs.
- A wrapper function that must forward all arguments to another function without knowing what they are.

Without `*args` and `**kwargs`, you'd need to define a separate function for every possible number of arguments — impossible for truly flexible APIs.

### Mental Model (Intuition First)

**`*args`** is like a **box** you hand to someone and say: "Put however many items you want in here." When they open the box, they find a tuple of everything that was tossed in.

**`**kwargs`** is like a **labeled filing cabinet**. Each drawer has a name (key) and contents (value). You can add as many labeled drawers as you want.

**Unpacking (`*` and `**`)** is the reverse — you take items OUT of a box or filing cabinet and spread them on the table as individual pieces.

### Visual Explanation

```
COLLECTING (in function definition):

def func(a, b, *args, **kwargs):

Call: func(1, 2, 3, 4, 5, x=10, y=20)

Parameter assignment:
  a = 1              ← first positional
  b = 2              ← second positional
  args = (3, 4, 5)   ← remaining positionals → tuple
  kwargs = {'x': 10, 'y': 20}  ← keyword args → dict

Memory:
+--------+---+---+-----------+------------------------+
| a = 1  | b = 2 | args =    | kwargs =               |
|        |       | (3, 4, 5) | {'x': 10, 'y': 20}    |
+--------+---+---+-----------+------------------------+
```

```
UNPACKING (in function call):

numbers = [10, 20, 30]
config = {'sep': '-', 'end': '!\n'}

print(*numbers, **config)
  ↓ unpacks to ↓
print(10, 20, 30, sep='-', end='!\n')

Output: 10-20-30!
```

### When To Use It

| Feature      | Use When                                                    |
|--------------|-------------------------------------------------------------|
| `*args`      | Function should accept any number of positional arguments   |
| `**kwargs`   | Function should accept any number of named options          |
| `*` unpack   | You have a list/tuple and need to pass items as separate args |
| `**` unpack  | You have a dict and need to pass it as keyword arguments    |

### When NOT To Use It

- **Don't use `*args`/`**kwargs` when the arguments are known and fixed.** Named parameters are clearer: `def add(a, b)` is better than `def add(*args)` when you always need exactly two numbers.
- **Don't use `**kwargs` to avoid defining proper parameters.** It hides what the function actually expects, making code harder to read and debug.
- **Don't unpack untrusted dictionaries into function calls** — unexpected keys cause `TypeError`.

### Comparison With Similar Concepts

| Concept            | Direction   | Syntax     | Result Type | Used In            |
|--------------------|-------------|------------|-------------|--------------------|
| `*args`            | Collect     | Definition | `tuple`     | Function signature |
| `**kwargs`         | Collect     | Definition | `dict`      | Function signature |
| `*iterable`        | Spread      | Call site  | Individual args | Function call  |
| `**dictionary`     | Spread      | Call site  | Keyword args    | Function call  |

---

## 2️⃣ Syntax & Structure

### Canonical Syntax

```python
# DEFINITION: Collecting arguments
def function_name(regular, *args, **kwargs):
    # regular → standard positional parameter
    # args   → tuple of extra positional arguments
    # kwargs → dict of extra keyword arguments
    pass
```

```python
# CALL: Unpacking arguments
my_list = [1, 2, 3]
my_dict = {'key': 'value'}

function_name(*my_list)     # Unpacks list → positional args
function_name(**my_dict)    # Unpacks dict → keyword args
function_name(*my_list, **my_dict)  # Both at once
```

### Mandatory Line-by-Line Breakdown

**Parameter Ordering Rule (STRICT):**

```python
def func(positional, default=value, *args, keyword_only, **kwargs):
#        ──────────  ─────────────  ─────  ────────────  ────────
#            1             2          3         4            5
```

1. **Regular positional parameters** — required, filled by position.
2. **Parameters with defaults** — optional, filled by position or name.
3. **`*args`** — catches all remaining positional arguments. Everything after `*args` in the definition becomes **keyword-only** (must be passed by name).
4. **Keyword-only parameters** — appear after `*args`, must be passed as `name=value`.
5. **`**kwargs`** — catches all remaining keyword arguments. Must be **last**.

**Violating this order causes a `SyntaxError`.**

**Common Beginner Mistakes:**

- Putting `**kwargs` before `*args` → `SyntaxError`.
- Forgetting that `*args` produces a **tuple** (immutable), not a list.
- Trying to use `args` and `kwargs` as variable names without the `*`/`**` — the names `args` and `kwargs` are just conventions. `*stuff` and `**options` work identically.

**Runtime behavior:**

- When the function is called, Python first fills regular parameters left-to-right.
- Any leftover positional arguments are packed into the `args` tuple.
- Any leftover keyword arguments are packed into the `kwargs` dict.
- If there are leftover arguments and no `*args`/`**kwargs` to catch them → `TypeError`.

---

## 3️⃣ Example 1 — Foundational: *args and **kwargs

### Goal

Demonstrate collecting and using variable arguments in the simplest meaningful way.

```python
# A function that can add ANY number of numbers
def add_all(*args):
    """Sum all positional arguments."""
    # args is a tuple: (1, 2, 3, ...) 
    total = 0
    for num in args:
        total += num
    return total

# Call with different numbers of arguments
print(add_all(1, 2))              # Two args
print(add_all(1, 2, 3, 4, 5))    # Five args
print(add_all(10))                # One arg
print(add_all())                  # Zero args — args is an empty tuple
```

### Step-by-Step Runtime Walkthrough

**Call: `add_all(1, 2, 3, 4, 5)`**

1. Python sees 5 positional arguments but no regular parameters to fill.
2. All 5 are packed into `args = (1, 2, 3, 4, 5)`.
3. `total = 0`.
4. Loop iterates: `total` becomes 1, then 3, then 6, then 10, then 15.
5. Returns `15`.

**Call: `add_all()`**

1. No arguments passed. `args = ()` (empty tuple).
2. `total = 0`. Loop body never executes.
3. Returns `0`.

### Runtime Visualization

```
add_all(1, 2, 3, 4, 5)

Stack Frame:
+----------------------------+
| args = (1, 2, 3, 4, 5)    |
| total = 0                 |
+----------------------------+

Iteration:
  total = 0 + 1 = 1
  total = 1 + 2 = 3
  total = 3 + 3 = 6
  total = 6 + 4 = 10
  total = 10 + 5 = 15

Return: 15
```

### **kwargs Example

```python
# A function that builds a user profile from any named attributes
def build_profile(first, last, **kwargs):
    """Build a dict with name and any extra info."""
    # kwargs is a dict of all extra keyword arguments
    profile = {
        'first_name': first,
        'last_name': last,
    }
    # Merge extra info into the profile
    profile.update(kwargs)
    return profile

# Call with different keyword arguments
user = build_profile('Ada', 'Lovelace', field='math', born=1815)
print(user)
```

**Walkthrough:**

1. `first = 'Ada'`, `last = 'Lovelace'` — filled by position.
2. `field='math'` and `born=1815` are extra keyword arguments → `kwargs = {'field': 'math', 'born': 1815}`.
3. `profile` starts as `{'first_name': 'Ada', 'last_name': 'Lovelace'}`.
4. `profile.update(kwargs)` merges in the extra keys.
5. Final `profile = {'first_name': 'Ada', 'last_name': 'Lovelace', 'field': 'math', 'born': 1815}`.

### Output

```
3
15
10
0
{'first_name': 'Ada', 'last_name': 'Lovelace', 'field': 'math', 'born': 1815}
```

---

## 4️⃣ Example 2 — Real-World: Unpacking & Flexible APIs

### Goal

Show unpacking operators in realistic scenarios: forwarding arguments, merging configurations, and building flexible wrapper functions.

```python
# --- Unpacking a list into function arguments ---

def greet(first, last, title="Mr."):
    """Formal greeting."""
    return f"Hello, {title} {first} {last}!"

# We have data in a list and a dict
name_parts = ["Grace", "Hopper"]
options = {"title": "Admiral"}

# Unpack them into the function call
print(greet(*name_parts, **options))
# Equivalent to: greet("Grace", "Hopper", title="Admiral")
```

```python
# --- Forwarding all arguments through a wrapper ---

def log_call(func):
    """A wrapper that logs any function call, regardless of its signature."""
    def wrapper(*args, **kwargs):
        # *args and **kwargs capture EVERYTHING the caller passes
        print(f"Calling {func.__name__} with args={args}, kwargs={kwargs}")

        # Forward ALL captured arguments to the original function
        result = func(*args, **kwargs)

        print(f"{func.__name__} returned {result}")
        return result
    return wrapper

# Apply the wrapper
@log_call
def multiply(a, b):
    return a * b

@log_call
def power(base, exp, mod=None):
    if mod:
        return (base ** exp) % mod
    return base ** exp

# These calls work regardless of how many args each function takes
multiply(6, 7)
power(2, 10, mod=100)
```

### Deep Explanation

- **`*name_parts`** unpacks `["Grace", "Hopper"]` into two separate positional arguments. Without the `*`, the entire list would be passed as a single argument.
- **`**options`** unpacks `{"title": "Admiral"}` into `title="Admiral"`. Without `**`, the dict itself would be passed as an argument.
- **The wrapper pattern** (`*args, **kwargs` in definition + `func(*args, **kwargs)` in call) is the **most important use case** for these features. It lets you write functions that work with ANY function signature — critical for decorators, middleware, and frameworks.
- **Why this matters for decorators:** The `wrapper` function doesn't know or care what parameters `multiply` or `power` expect. It blindly captures everything and forwards it. This is how decorators can wrap any function.

### Runtime Flow Diagram

```
multiply(6, 7)
       ↓
wrapper(*args, **kwargs)
  args = (6, 7)
  kwargs = {}
       ↓
  print("Calling multiply with args=(6, 7), kwargs={}")
       ↓
  result = multiply(6, 7)   ← original function
  result = 42
       ↓
  print("multiply returned 42")
       ↓
  return 42


power(2, 10, mod=100)
       ↓
wrapper(*args, **kwargs)
  args = (2, 10)
  kwargs = {'mod': 100}
       ↓
  print("Calling power with args=(2, 10), kwargs={'mod': 100}")
       ↓
  result = power(2, 10, mod=100)   ← original function
  result = (2^10) % 100 = 1024 % 100 = 24
       ↓
  print("power returned 24")
       ↓
  return 24
```

### Merging Dictionaries with Unpacking

```python
# Combining configuration dictionaries
defaults = {"color": "blue", "size": 12, "font": "Arial"}
user_prefs = {"color": "red", "size": 16}

# ** unpacking merges dicts — later values override earlier ones
final_config = {**defaults, **user_prefs}
print(final_config)
# {'color': 'red', 'size': 16, 'font': 'Arial'}
# 'color' and 'size' overridden by user_prefs, 'font' kept from defaults
```

### Output

```
Hello, Admiral Grace Hopper!
Calling multiply with args=(6, 7), kwargs={}
multiply returned 42
Calling power with args=(2, 10), kwargs={'mod': 100}
power returned 24
{'color': 'red', 'size': 16, 'font': 'Arial'}
```

---
---

# 2. Higher-Order Functions & Decorators

---

## Concept Overview

### Definition

A **higher-order function** is a function that does at least one of:
1. **Takes a function as an argument** (e.g., `map(func, iterable)`)
2. **Returns a function as its result** (e.g., a decorator factory)

A **closure** is an inner function that **remembers variables from its enclosing scope** even after the outer function has finished executing.

A **decorator** is a specific pattern that uses both concepts: it's a higher-order function that takes a function, wraps it in a closure, and returns the wrapper — modifying the original function's behavior without changing its code.

### Why It Exists

Without higher-order functions:
- You'd duplicate logic everywhere. Want to log 10 different functions? Write logging code inside each one.
- You couldn't pass behavior as data. Sorting by custom criteria, filtering with custom rules, mapping with custom transformations — all impossible.

Without decorators:
- Cross-cutting concerns (logging, timing, authentication, caching) would be copy-pasted into every function.
- Changing the logging format would mean editing dozens of functions.

### Mental Model (Intuition First)

**Higher-order function:** A **manager** who delegates work. The manager doesn't do the task — they decide *who* does it and *how*. `sorted(data, key=len)` is a manager that delegates comparison to `len`.

**Closure:** A **backpack**. When an inner function is created inside an outer function, it packs the outer function's variables into its backpack. Even after the outer function ends, the inner function still carries those variables.

**Decorator:** A **gift wrapper**. You hand in a plain box (function), and the wrapper adds a bow, ribbon, and tag (extra behavior) without opening or changing the box itself.

### Visual Explanation

```
CLOSURE — How variables are "remembered":

def outer(x):
    def inner(y):
        return x + y    ← inner "remembers" x from outer's scope
    return inner

add_five = outer(5)

Memory after outer(5) returns:
+---------------------------+
| add_five → inner function |
|   closure: { x: 5 }      |  ← x is "packed in the backpack"
+---------------------------+

outer's stack frame is GONE, but x=5 lives on in the closure.

add_five(3) → 5 + 3 → 8
```

```
DECORATOR — Wrapping a function:

@decorator
def original():       
    ...

Is equivalent to:
original = decorator(original)

Before decoration:
  original → <original function>

After decoration:
  original → <wrapper function>
                  ↓ (internally calls)
              <original function>
```

### When To Use It

| Pattern    | Use When                                                        |
|------------|-----------------------------------------------------------------|
| Higher-order functions | Passing behavior as a parameter (sorting, filtering, mapping) |
| Closures   | Creating specialized functions from a template (e.g., `make_multiplier(3)`) |
| Decorators | Adding cross-cutting behavior: logging, timing, auth, caching, validation |

### When NOT To Use It

- **Don't use decorators for core business logic.** Decorators should add *auxiliary* behavior (logging, timing), not implement the main algorithm.
- **Don't stack too many decorators.** More than 2-3 becomes hard to debug — the call chain gets deeply nested.
- **Don't use closures when a class would be clearer.** If the closure needs to manage complex mutable state, a class with methods is more readable.

### Comparison With Similar Concepts

| Concept              | What It Does                              | Returns          |
|----------------------|-------------------------------------------|------------------|
| Regular function     | Performs a task                            | A value          |
| Higher-order function| Operates on functions                     | A value or function |
| Closure              | Captures enclosing scope                  | A function with memory |
| Decorator            | Wraps a function to modify behavior       | A wrapped function |
| Class                | Bundles state and behavior                | An object        |

---

## 2️⃣ Syntax & Structure

### Canonical Syntax — Higher-Order Function

```python
# A function that TAKES a function as an argument
def apply_operation(func, a, b):
    return func(a, b)

# Usage:
result = apply_operation(max, 10, 20)  # Passes the built-in 'max' function
```

### Canonical Syntax — Closure

```python
def outer_function(captured_value):
    """The outer function creates a scope with 'captured_value'."""

    def inner_function(x):
        """The inner function uses 'captured_value' from the enclosing scope."""
        return captured_value + x

    return inner_function  # Return the function itself, not a call result
```

### Canonical Syntax — Decorator

```python
import functools

def my_decorator(func):
    """A decorator that wraps 'func' with extra behavior."""

    @functools.wraps(func)  # Preserves original function's __name__ and __doc__
    def wrapper(*args, **kwargs):
        # --- Code BEFORE the original function ---
        print("Before")

        result = func(*args, **kwargs)  # Call the original function

        # --- Code AFTER the original function ---
        print("After")

        return result

    return wrapper

# Apply the decorator
@my_decorator
def say_hello(name):
    """Greet someone."""
    print(f"Hello, {name}!")
```

### Mandatory Line-by-Line Breakdown

**Decorator anatomy:**

1. **`def my_decorator(func):`** — The decorator is a function that receives the function being decorated.
2. **`@functools.wraps(func)`** — Critical metadata preservation. Without this, `say_hello.__name__` would return `"wrapper"` instead of `"say_hello"`, and `say_hello.__doc__` would return the wrapper's docstring. This line copies the original function's metadata onto the wrapper.
3. **`def wrapper(*args, **kwargs):`** — The replacement function. Uses `*args, **kwargs` to accept ANY arguments, making the decorator work with any function signature.
4. **`result = func(*args, **kwargs)`** — Calls the original function with all forwarded arguments.
5. **`return wrapper`** — The decorator returns the wrapper, which replaces the original function.

**What `@my_decorator` does at runtime:**

```python
@my_decorator
def say_hello(name):
    ...

# Is EXACTLY equivalent to:
def say_hello(name):
    ...
say_hello = my_decorator(say_hello)
```

**Common Beginner Mistakes:**

- Forgetting `@functools.wraps(func)` — debugging becomes confusing when function names are wrong.
- Forgetting to `return result` inside the wrapper — the decorated function silently returns `None`.
- Calling the function inside the decorator definition instead of returning it: `return wrapper()` vs `return wrapper`. The parentheses make a huge difference.
- Forgetting `*args, **kwargs` in the wrapper — the decorator breaks for functions with different signatures.

---

## 3️⃣ Example 1 — Foundational: Closures & First Decorators

### Goal

Build understanding from the ground up: first higher-order functions, then closures, then a simple decorator.

### Part A: Higher-Order Function

```python
# Functions are objects — they can be passed around like any value

def shout(text):
    """Convert text to uppercase with exclamation."""
    return text.upper() + "!"

def whisper(text):
    """Convert text to lowercase with ellipsis."""
    return text.lower() + "..."

def speak(func, message):
    """A higher-order function: takes a function and applies it."""
    # 'func' is whatever function was passed in
    return func(message)

# Pass different functions to get different behavior
print(speak(shout, "hello"))
print(speak(whisper, "HELLO"))
```

**Walkthrough:**

1. `speak(shout, "hello")` — `func` is bound to the `shout` function object. `func("hello")` calls `shout("hello")` → `"HELLO!"`.
2. `speak(whisper, "HELLO")` — `func` is bound to `whisper`. `func("HELLO")` calls `whisper("HELLO")` → `"hello..."`.

**Key insight:** We passed `shout` and `whisper` without parentheses. `shout` is the function object. `shout()` would *call* it. Big difference.

### Part B: Closure

```python
# A closure: inner function remembers the outer function's variables

def make_multiplier(factor):
    """Return a function that multiplies its input by 'factor'."""

    def multiplier(x):
        # 'factor' is NOT defined here — it comes from the enclosing scope
        return x * factor

    return multiplier  # Return the inner function (not a call)

# Create specialized functions
double = make_multiplier(2)
triple = make_multiplier(3)

print(double(10))   # 10 * 2 = 20
print(triple(10))   # 10 * 3 = 30
print(double(7))    # 7 * 2 = 14
```

### Step-by-Step Runtime Walkthrough

1. `make_multiplier(2)` is called. `factor = 2`. Python creates `multiplier` function inside this scope.
2. `multiplier` references `factor` — Python notes this as a **closure variable**.
3. `make_multiplier` returns `multiplier`. The stack frame for `make_multiplier` is destroyed — BUT `factor = 2` is preserved in the closure.
4. `double` now points to `multiplier` with `factor = 2` baked in.
5. `make_multiplier(3)` is called separately. A NEW `multiplier` is created with `factor = 3`.
6. `triple` points to this new `multiplier` with `factor = 3`.
7. `double(10)` → `10 * 2` → `20`. The `2` comes from the closure.
8. `triple(10)` → `10 * 3` → `30`. The `3` comes from a different closure.

### Runtime Visualization

```
After make_multiplier(2):
+----------------------------------+
| double → multiplier function     |
|   closure: { factor: 2 }        |
+----------------------------------+

After make_multiplier(3):
+----------------------------------+
| triple → multiplier function     |
|   closure: { factor: 3 }        |
+----------------------------------+

double and triple are DIFFERENT function objects
with DIFFERENT closures, even though they came
from the same template.

double(10):
  Stack: [multiplier: x=10]
  Closure: factor=2
  Return: 10 * 2 = 20

triple(10):
  Stack: [multiplier: x=10]
  Closure: factor=3
  Return: 10 * 3 = 30
```

### Part C: First Decorator

```python
import functools

def shout_decorator(func):
    """Decorator that converts any function's string return to uppercase."""

    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        # Call the original function
        result = func(*args, **kwargs)
        # Modify the result
        return result.upper() if isinstance(result, str) else result

    return wrapper

@shout_decorator
def greet(name):
    """Return a greeting for the given name."""
    return f"hello, {name}. welcome!"

# The decorator transforms the output
print(greet("alice"))

# Metadata is preserved thanks to @functools.wraps
print(greet.__name__)
print(greet.__doc__)
```

**Walkthrough:**

1. `@shout_decorator` triggers: `greet = shout_decorator(greet)`.
2. Inside `shout_decorator`: `func` is the original `greet`. A `wrapper` is created that calls `func` and uppercases the result.
3. `@functools.wraps(func)` copies `greet`'s `__name__` ("greet") and `__doc__` onto `wrapper`.
4. `shout_decorator` returns `wrapper`. Now `greet` points to `wrapper`.
5. `greet("alice")` → calls `wrapper("alice")` → calls original `func("alice")` → returns `"hello, alice. welcome!"` → uppercased to `"HELLO, ALICE. WELCOME!"`.

### Output

```
HELLO!
hello...
20
30
14
HELLO, ALICE. WELCOME!
greet
Return a greeting for the given name.
```

---

## 4️⃣ Example 2 — Real-World: Logging, Timing, Caching & Stacking

### Goal

Show decorators as they appear in professional codebases: practical utilities that you'd actually use in production.

### Timing Decorator

```python
import functools
import time

def timer(func):
    """Decorator that measures and prints execution time."""

    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()          # High-precision timer
        result = func(*args, **kwargs)       # Run the original function
        elapsed = time.perf_counter() - start
        print(f"⏱ {func.__name__} took {elapsed:.4f} seconds")
        return result

    return wrapper

@timer
def slow_sum(n):
    """Sum numbers from 0 to n (slowly, for demo)."""
    total = 0
    for i in range(n):
        total += i
    return total

print(slow_sum(1_000_000))
```

### Validation Decorator

```python
import functools

def validate_positive(func):
    """Decorator that ensures all numeric arguments are positive."""

    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        # Check positional arguments
        for i, arg in enumerate(args):
            if isinstance(arg, (int, float)) and arg < 0:
                raise ValueError(
                    f"Argument {i} to {func.__name__} must be positive, got {arg}"
                )
        # Check keyword arguments
        for key, val in kwargs.items():
            if isinstance(val, (int, float)) and val < 0:
                raise ValueError(
                    f"Argument '{key}' to {func.__name__} must be positive, got {val}"
                )
        return func(*args, **kwargs)

    return wrapper

@validate_positive
def calculate_area(width, height):
    """Calculate rectangle area. Both dimensions must be positive."""
    return width * height

print(calculate_area(5, 10))       # Works fine

try:
    print(calculate_area(-3, 10))  # Raises ValueError
except ValueError as e:
    print(f"Error: {e}")
```

### Memoization (Caching) Decorator

```python
import functools

def memoize(func):
    """Decorator that caches results to avoid redundant computation."""
    cache = {}  # This dict lives in the closure — persists across calls

    @functools.wraps(func)
    def wrapper(*args):
        if args in cache:
            print(f"  Cache hit for {func.__name__}{args}")
            return cache[args]

        print(f"  Computing {func.__name__}{args}...")
        result = func(*args)
        cache[args] = result  # Store result for future calls
        return result

    return wrapper

@memoize
def fibonacci(n):
    """Compute the nth Fibonacci number recursively."""
    if n <= 1:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

# Without memoization, fibonacci(35) would make ~18 million calls
# With memoization, it makes only 36 calls
print(f"fibonacci(10) = {fibonacci(10)}")
```

### Deep Explanation

- **Timer:** Captures the time before and after the function runs. `time.perf_counter()` is the most precise timer available. This pattern is used in profiling and performance monitoring.

- **Validation:** Checks arguments *before* the function runs. If validation fails, the function never executes. This is the **guard clause** pattern — fail fast with a clear error message. Used extensively in web frameworks for input validation.

- **Memoization:** The `cache` dictionary lives in the closure of `wrapper`. It persists across all calls to the decorated function. When `fibonacci(10)` calls `fibonacci(9)` and `fibonacci(8)`, and `fibonacci(9)` also calls `fibonacci(8)` — the second call to `fibonacci(8)` hits the cache instead of recomputing.

  **Note:** Python's standard library provides `@functools.lru_cache` which does this automatically with additional features (max cache size, cache statistics).

### Stacking Decorators

```python
# Decorators can be stacked — they apply bottom-to-top

@timer              # 2nd: wraps the validated version
@validate_positive  # 1st: wraps the original function
def safe_power(base, exp):
    """Compute base^exp safely."""
    return base ** exp

# Execution order:
# timer.wrapper → validate_positive.wrapper → safe_power
print(safe_power(2, 10))
```

**Stacking order matters:**

```
@A
@B
def func():
    ...

# Equivalent to:
func = A(B(func))

# Call chain:
A's wrapper
  → B's wrapper
    → original func
```

### Runtime Flow Diagram

```
safe_power(2, 10)
       ↓
timer.wrapper(2, 10)
  start = time.perf_counter()
       ↓
  validate_positive.wrapper(2, 10)
    Check: 2 > 0 ✓, 10 > 0 ✓
       ↓
    safe_power(2, 10)  ← original
    return 1024
       ↓
  validate_positive.wrapper returns 1024
       ↓
  elapsed = time.perf_counter() - start
  print("⏱ safe_power took 0.0001 seconds")
  return 1024
       ↓
print(1024)
```

### Output

```
⏱ slow_sum took 0.0XXX seconds
499999500000
50
Error: Argument 0 to calculate_area must be positive, got -3
  Computing fibonacci(10)...
  Computing fibonacci(9)...
  Computing fibonacci(8)...
  Computing fibonacci(7)...
  Computing fibonacci(6)...
  Computing fibonacci(5)...
  Computing fibonacci(4)...
  Computing fibonacci(3)...
  Computing fibonacci(2)...
  Computing fibonacci(1)...
  Computing fibonacci(0)...
  Cache hit for fibonacci(1)
  Cache hit for fibonacci(2)
  Cache hit for fibonacci(3)
  Cache hit for fibonacci(4)
  Cache hit for fibonacci(5)
  Cache hit for fibonacci(6)
  Cache hit for fibonacci(7)
  Cache hit for fibonacci(8)
fibonacci(10) = 55
⏱ safe_power took 0.0000 seconds
1024
```

---
---

# 3. Modern Project Management & Environment

---

## Concept Overview

### Definition

**Modern Python project management** encompasses the tools and practices that keep your project organized, reproducible, and secure:

- **Virtual Environments (`.venv`)** — Isolated Python installations per project.
- **Dependency Management (`uv`)** — Installing, tracking, and locking package versions.
- **Secrets Management (`.env`)** — Keeping sensitive data out of source code.
- **Debugging (VS Code)** — Configuring your editor to step through code interactively.

### Why It Exists

Without these practices:

- **No virtual environments:** Installing a package for Project A breaks Project B because they need different versions of the same library. Your system Python becomes a mess of conflicting dependencies.
- **No dependency locking:** Your code works on your machine but fails on a colleague's because they got a newer (incompatible) version of a library.
- **No secrets management:** API keys get committed to Git, pushed to GitHub, and scraped by bots within minutes. Real-world consequence: compromised accounts, stolen data, unexpected bills.
- **No debugger:** You litter code with `print()` statements, forget to remove them, and spend hours guessing where bugs are.

### Mental Model (Intuition First)

**Virtual environment** = A **private toolbox** for each project. Project A has its own hammer (library v1.0), Project B has its own hammer (library v2.0). They don't share, so they can't break each other.

**`uv.lock`** = A **recipe card** that lists the exact brand and quantity of every ingredient. Anyone with this card can recreate the exact same dish.

**`.env` file** = A **safe** in your office. The secret codes are inside the safe, not written on the whiteboard (source code) for everyone to see.

**Debugger** = A **slow-motion camera** for your code. You can pause execution at any point, inspect every variable, and step through one line at a time.

### Visual Explanation

```
WITHOUT virtual environments:

System Python
├── numpy 1.21 (needed by Project A)
├── numpy 1.24 (needed by Project B) ← CONFLICT! Only one can exist
├── flask 2.0
├── django 4.0
└── (everything mixed together)

WITH virtual environments:

System Python (clean)

Project A/.venv/
├── numpy 1.21
└── flask 2.0

Project B/.venv/
├── numpy 1.24
└── django 4.0

No conflicts. Each project is isolated.
```

```
Secrets flow:

.env file (NOT in Git):
+---------------------------+
| API_KEY=sk-abc123...      |
| DB_PASSWORD=supersecret   |
+---------------------------+
        ↓ loaded by python-dotenv
        ↓
Your Python code:
  key = os.getenv("API_KEY")
        ↓
  Uses key to call API
        ↓
  Key NEVER appears in source code
  Key NEVER appears in Git history
```

### When To Use It

| Practice              | When                                      |
|-----------------------|-------------------------------------------|
| Virtual environments  | **Every project.** No exceptions.         |
| Dependency locking    | **Every project** with external packages. |
| `.env` for secrets    | Any project with API keys, passwords, tokens. |
| Debugger              | When `print()` debugging isn't enough — complex bugs, unfamiliar code. |

### When NOT To Use It

- **Don't skip virtual environments** for "quick scripts" — it's a habit that prevents future pain.
- **Don't store non-secret config in `.env`** if it should be version-controlled. Use `config.py` or `pyproject.toml` for non-sensitive settings.
- **Don't rely solely on the debugger** — write tests first. The debugger is for investigation, not a substitute for automated testing.

### Comparison With Similar Concepts

| Tool          | Purpose                    | Alternative         | Why Prefer This One          |
|---------------|----------------------------|---------------------|------------------------------|
| `venv`        | Create virtual environments| `conda`, `virtualenv` | Built into Python, lightweight |
| `uv`          | Fast package management    | `pip`, `poetry`     | 10-100x faster than pip      |
| `pyproject.toml` | Project configuration   | `setup.py`, `setup.cfg` | Modern standard (PEP 621) |
| `uv.lock`     | Lock exact versions        | `requirements.txt`  | Deterministic, cross-platform |
| `.env` + `python-dotenv` | Secrets management | Environment variables directly | Easier local development |

---

## 2️⃣ Syntax & Structure

### Virtual Environment Setup with `uv`

```bash
# Install uv (if not already installed)
# On macOS/Linux:
curl -LsSf https://astral.sh/uv/install.sh | sh

# On Windows:
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

# Initialize a new project
uv init my_project
cd my_project

# This creates:
# my_project/
# ├── pyproject.toml    ← project metadata & dependencies
# ├── .python-version   ← pinned Python version
# └── src/
#     └── my_project/
#         └── __init__.py

# Create a virtual environment
uv venv

# This creates a .venv/ directory with an isolated Python installation

# Activate the virtual environment
# macOS/Linux:
source .venv/bin/activate

# Windows:
.venv\Scripts\activate

# Your prompt changes to show the active environment:
# (.venv) $

# Add a dependency
uv add requests

# This does THREE things:
# 1. Installs 'requests' into .venv/
# 2. Adds 'requests' to pyproject.toml under [project.dependencies]
# 3. Updates uv.lock with the exact version and all sub-dependencies

# Install all dependencies from lock file (e.g., on a new machine)
uv sync

# Deactivate when done
deactivate
```

### Mandatory Line-by-Line Breakdown

- **`uv init my_project`** — Creates a new project directory with a `pyproject.toml` file. This is the modern replacement for `setup.py`.
- **`uv venv`** — Creates a `.venv/` directory containing a complete, isolated Python installation. Packages installed here don't affect your system Python.
- **`source .venv/bin/activate`** — Modifies your shell's `PATH` so that `python` and `pip` point to the `.venv` versions. This is temporary — only affects the current terminal session.
- **`uv add requests`** — Installs the package AND records it as a dependency. This is better than `pip install requests` because it also updates your project files.
- **`uv sync`** — Reads `uv.lock` and installs the exact versions listed. This ensures every developer and every server gets identical dependencies.
- **`deactivate`** — Restores your shell's `PATH` to normal. The `.venv` still exists; it's just not active.

### `pyproject.toml` Structure

```toml
[project]
name = "my_project"
version = "0.1.0"
description = "My awesome project"
requires-python = ">=3.11"

# Dependencies are listed here
dependencies = [
    "requests>=2.31.0",
    "python-dotenv>=1.0.0",
]

[project.optional-dependencies]
# Dev-only dependencies (testing, linting)
dev = [
    "pytest>=7.0",
    "ruff>=0.1.0",
]
```

### `.env` File and `python-dotenv`

```bash
# .env — Store secrets here (NEVER commit this file)
API_KEY=sk-abc123def456ghi789
DATABASE_URL=postgresql://user:password@localhost:5432/mydb
DEBUG=true
```

```python
# config.py — Load secrets safely
import os
from dotenv import load_dotenv

# Load variables from .env file into environment
load_dotenv()

# Access them via os.getenv()
API_KEY = os.getenv("API_KEY")
DATABASE_URL = os.getenv("DATABASE_URL")
DEBUG = os.getenv("DEBUG", "false").lower() == "true"  # Default to false
```

### `.gitignore` (Critical Security)

```gitignore
# Virtual environment — don't commit installed packages
.venv/

# Environment secrets — NEVER commit
.env

# Python bytecode cache
__pycache__/
*.pyc

# IDE settings (optional, but common)
.vscode/
```

---

## 3️⃣ Example 1 — Foundational: Virtual Environments & uv

### Goal

Walk through creating a project from scratch with proper isolation and dependency management.

### Complete Workflow

```bash
# Step 1: Create and enter project
uv init weather_app
cd weather_app

# Step 2: Create virtual environment
uv venv

# Step 3: Activate it
source .venv/bin/activate    # macOS/Linux
# .venv\Scripts\activate     # Windows

# Step 4: Add dependencies
uv add requests python-dotenv

# Step 5: Verify installation
uv pip list
```

### What the file system looks like after setup

```
weather_app/
├── .venv/                    ← Isolated Python + installed packages
│   ├── bin/                  ← (or Scripts/ on Windows)
│   │   ├── python            ← This project's Python
│   │   ├── pip
│   │   └── activate
│   └── lib/
│       └── python3.12/
│           └── site-packages/
│               ├── requests/
│               └── dotenv/
├── src/
│   └── weather_app/
│       └── __init__.py
├── pyproject.toml            ← Project config + dependency list
├── uv.lock                   ← Exact locked versions
├── .python-version           ← Python version pin
└── .gitignore
```

### Step-by-Step Explanation

1. **`uv init`** scaffolds the project with modern Python standards.
2. **`uv venv`** creates `.venv/` — a complete Python installation. When activated, `python` refers to `.venv/bin/python`, not your system Python.
3. **`uv add requests python-dotenv`** does three things simultaneously:
   - Downloads and installs both packages (and their dependencies) into `.venv/`.
   - Adds them to `pyproject.toml` under `[project.dependencies]`.
   - Generates/updates `uv.lock` with exact versions (e.g., `requests==2.31.0`, not just `requests`).
4. **`uv.lock`** is the key to reproducibility. When a teammate clones your repo and runs `uv sync`, they get the *exact same versions* you have.

### Runtime Visualization

```
Before activation:
$ which python
/usr/bin/python3          ← System Python

After activation:
(.venv) $ which python
/home/user/weather_app/.venv/bin/python   ← Project Python

Packages installed in .venv are INVISIBLE to system Python
and to other projects' .venv directories.
```

### Why `uv` over `pip`?

```
Speed comparison (approximate):
pip install numpy:     ~15 seconds
uv add numpy:         ~0.5 seconds

uv is written in Rust and resolves dependencies in parallel.
It's a drop-in improvement — same packages, same PyPI, just faster.
```

---

## 4️⃣ Example 2 — Real-World: Secrets, .env, and VS Code Debugging

### Goal

Build a realistic mini-application that uses secrets management and show how to debug it in VS Code.

### Project Files

**`.env`** (NOT committed to Git)

```bash
# .env — Local secrets
WEATHER_API_KEY=abc123fake_key_for_demo
DEFAULT_CITY=Vancouver
```

**`app.py`**

```python
# app.py — A weather lookup app (simplified)
import os
from dotenv import load_dotenv

# Step 1: Load environment variables from .env file
# This reads .env and adds its contents to os.environ
load_dotenv()

# Step 2: Read secrets from environment (NOT hardcoded)
API_KEY = os.getenv("WEATHER_API_KEY")
DEFAULT_CITY = os.getenv("DEFAULT_CITY", "London")  # Fallback default

def get_weather(city):
    """Simulate fetching weather data."""
    if not API_KEY:
        raise RuntimeError("WEATHER_API_KEY not set! Check your .env file.")

    # In a real app, this would call an API using 'requests'
    # requests.get(f"https://api.weather.com?key={API_KEY}&city={city}")

    # Simulated response for demo
    return {
        "city": city,
        "temp": 18,
        "condition": "Partly Cloudy",
        "api_key_used": API_KEY[:6] + "..."  # Never log full keys!
    }

def display_weather(data):
    """Format and print weather data."""
    print(f"Weather for {data['city']}:")
    print(f"  Temperature: {data['temp']}°C")
    print(f"  Condition:   {data['condition']}")

if __name__ == '__main__':
    city = input("Enter city (or press Enter for default): ").strip()
    if not city:
        city = DEFAULT_CITY

    weather = get_weather(city)
    display_weather(weather)
```

### Deep Explanation

- **`load_dotenv()`** reads the `.env` file line by line, parses `KEY=VALUE` pairs, and injects them into `os.environ`. After this call, `os.getenv("WEATHER_API_KEY")` works as if you had set the variable in your terminal.
- **`os.getenv("DEFAULT_CITY", "London")`** — The second argument is a fallback. If `DEFAULT_CITY` isn't in `.env` or the environment, it defaults to `"London"`.
- **`API_KEY[:6] + "..."`** — Never log or print full API keys. Show just enough to identify which key is in use.
- **The `if __name__` guard** ensures the interactive part only runs when you execute `app.py` directly, not when it's imported.

### `.gitignore` — The Security Gate

```gitignore
# This file IS committed to Git — it tells Git what to ignore

# CRITICAL: Never commit secrets
.env

# Virtual environment
.venv/

# Bytecode
__pycache__/
```

**What happens if you forget `.gitignore`:**

```
You commit .env → push to GitHub → bot scrapes your API key
→ your account is compromised within MINUTES.

This is not hypothetical. It happens constantly.
GitHub even has automated scanning that will email you
if it detects leaked secrets, but by then it may be too late.
```

### VS Code Debugging Configuration

**`.vscode/launch.json`**

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Debug Weather App",
            "type": "debugpy",
            "request": "launch",
            "program": "${workspaceFolder}/app.py",
            "console": "integratedTerminal",
            "python": "${workspaceFolder}/.venv/bin/python",
            "envFile": "${workspaceFolder}/.env",
            "justMyCode": true
        }
    ]
}
```

### Line-by-Line Breakdown of `launch.json`

- **`"type": "debugpy"`** — Uses Python's official debugger.
- **`"request": "launch"`** — Start a new process (vs. `"attach"` to an existing one).
- **`"program"`** — The file to run. `${workspaceFolder}` is the project root.
- **`"console": "integratedTerminal"`** — Uses VS Code's terminal so `input()` works.
- **`"python"`** — Points to the virtual environment's Python, ensuring the correct packages are available.
- **`"envFile"`** — Loads `.env` variables into the debug session automatically.
- **`"justMyCode": true`** — The debugger only stops in YOUR code, not inside library code. Set to `false` when debugging library behavior.

### How to Debug in VS Code

```
Step 1: Open app.py in VS Code

Step 2: Click in the gutter (left of line numbers) to set a BREAKPOINT
         → A red dot appears on that line
         → Set one on: weather = get_weather(city)

Step 3: Press F5 (or Run → Start Debugging)
         → The program starts and PAUSES at your breakpoint

Step 4: Inspect variables in the left panel:
         +---------------------------+
         | Variables                 |
         |   city = "Vancouver"     |
         |   API_KEY = "abc123..."  |
         |   DEFAULT_CITY = "Van.." |
         +---------------------------+

Step 5: Use debug controls:
         F10 = Step Over (execute line, move to next)
         F11 = Step Into (enter the function call)
         F5  = Continue (run until next breakpoint)
         Shift+F5 = Stop debugging

Step 6: Add WATCH expressions:
         → Right-click a variable → "Add to Watch"
         → Or type expressions like: len(city), API_KEY[:3]
         → Watch panel updates live as you step through code
```

### Runtime Flow Diagram

```
F5 pressed → VS Code launches .venv/bin/python app.py
       ↓
load_dotenv() reads .env
  os.environ["WEATHER_API_KEY"] = "abc123fake_key_for_demo"
  os.environ["DEFAULT_CITY"] = "Vancouver"
       ↓
API_KEY = "abc123fake_key_for_demo"
DEFAULT_CITY = "Vancouver"
       ↓
__name__ == "__main__" → True
       ↓
input("Enter city...") → user types "" (empty)
       ↓
city = "Vancouver" (from DEFAULT_CITY)
       ↓
● BREAKPOINT HIT — execution pauses
  You inspect: city="Vancouver", API_KEY="abc123..."
       ↓
F10 (Step Over) → get_weather("Vancouver") executes
       ↓
weather = {"city": "Vancouver", "temp": 18, ...}
  You inspect the dict in the Variables panel
       ↓
F10 → display_weather(weather) executes
       ↓
Output appears in terminal:
  Weather for Vancouver:
    Temperature: 18°C
    Condition:   Partly Cloudy
       ↓
Program ends. Debug session closes.
```

### Output

```
Enter city (or press Enter for default): 
Weather for Vancouver:
  Temperature: 18°C
  Condition:   Partly Cloudy
```

---
---

## Week 3 Summary

| Topic                          | Key Takeaway                                                                 |
|--------------------------------|------------------------------------------------------------------------------|
| `*args` / `**kwargs`           | Collect variable arguments into tuple/dict. Essential for flexible APIs and decorators. |
| Unpacking (`*` / `**`)         | Spread iterables/dicts into function arguments. The reverse of collecting.   |
| Parameter Order                | `positional, defaults, *args, keyword_only, **kwargs` — strict order.        |
| Higher-Order Functions         | Functions that take or return functions. Foundation for functional programming. |
| Closures                       | Inner functions that remember enclosing scope. The mechanism behind decorators. |
| Decorators                     | `@decorator` wraps a function to add behavior. Always use `@functools.wraps`. |
| Virtual Environments           | `.venv` isolates dependencies per project. Use for EVERY project.            |
| `uv`                           | Modern, fast package manager. `uv add`, `uv sync`, `uv.lock` for reproducibility. |
| Secrets (`.env`)               | Never hardcode secrets. Load from `.env` with `python-dotenv`. Add `.env` to `.gitignore`. |
| VS Code Debugging              | `launch.json` configures the debugger. Breakpoints, step-through, and variable inspection. |
