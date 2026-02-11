# Week 1 — Python Fundamentals & Core Concepts

---

# 1. Typing & Variables

---

## 1️⃣ Concept Overview

### Definition

In Python, a **variable** is a name that refers to a value stored in memory. Python is **dynamically typed** (you never declare a type — Python figures it out at runtime) and **strongly typed** (Python will not silently convert between incompatible types).

### Why It Exists

- Without variables, every value would be used once and discarded. Variables let you **store**, **reuse**, and **update** data.
- Dynamic typing removes boilerplate — you write less code. Strong typing catches bugs — `"5" + 3` raises an error instead of silently producing `"53"` or `8`.

### Mental Model (Intuition First)

Think of a variable as a **sticky note** you place on a box. The box is the actual data sitting in memory. The sticky note (variable name) just tells you where to find it. You can move the sticky note to a different box at any time — that's dynamic typing.

```
name ──► "Alice"      (a string object in memory)
age  ──► 30           (an integer object in memory)
```

If you write `age = "thirty"`, the sticky note `age` now points to a completely different box (a string). Python doesn't stop you — that's dynamic typing.

But if you write `"thirty" + 5`, Python **will** stop you — that's strong typing. It refuses to guess what you meant.

### Visual Explanation

```
Dynamic Typing in Action:

Step 1:  x = 10
         x ──► [ int: 10 ]

Step 2:  x = "hello"
         x ──► [ str: "hello" ]      (the int 10 is now orphaned, garbage collected)

Strong Typing in Action:

>>> "5" + 3
TypeError: can only concatenate str (not "int") to str

>>> "5" + str(3)
"53"          ← You must explicitly convert
```

### When To Use It

- **Always.** Every Python program uses variables.
- Use **snake_case** for regular variables: `user_name`, `total_count`.
- Use **UPPER_SNAKE_CASE** for constants: `MAX_RETRIES`, `API_BASE_URL`.

### When NOT To Use It

- Don't create variables you never use — it wastes memory and confuses readers.
- Don't use single-letter names except in tiny loops (`for i in range(10)` is fine; `x = get_database_connection()` is not).
- Don't reuse a variable name for a completely different purpose mid-function.

### Comparison With Similar Concepts

| Language   | Typing Style              | Example                        |
|------------|---------------------------|--------------------------------|
| Python     | Dynamic + Strong          | `x = 5` (no type declaration)  |
| JavaScript | Dynamic + Weak            | `"5" + 3` → `"53"` (coerced)  |
| Java       | Static + Strong           | `int x = 5;` (type required)   |
| C          | Static + Weak             | Allows many implicit casts     |

---

## 2️⃣ Syntax & Structure

### Canonical Syntax

```python
# Variable assignment
variable_name = value

# Constants (by convention — Python doesn't enforce it)
CONSTANT_NAME = value

# Multiple assignment
a, b, c = 1, 2, 3

# Type checking at runtime
type(variable_name)
isinstance(variable_name, expected_type)
```

### Line-by-Line Breakdown

- `variable_name = value` — The `=` is the **assignment operator**. It does NOT mean "equals" in the math sense. It means "make the name on the left point to the value on the right."
- `CONSTANT_NAME = value` — Python has **no true constants**. Writing in `UPPER_SNAKE_CASE` is a **convention** that tells other developers: "don't change this." Nothing in Python actually prevents reassignment.
- `a, b, c = 1, 2, 3` — This is **tuple unpacking**. Python creates three separate variables in one line. The number of names on the left **must** match the number of values on the right, or you get a `ValueError`.
- `type(x)` — Returns the **class** of the object `x` points to. Useful for debugging.
- `isinstance(x, int)` — Returns `True` or `False`. Preferred over `type(x) == int` because it also works with **inheritance** (a concept you'll learn later).

**Common Beginner Mistakes:**

- Writing `x = 5` and thinking `x` **is** the number 5. It's not — `x` is a label pointing to an integer object `5` in memory.
- Confusing `=` (assignment) with `==` (comparison).
- Naming a variable `list` or `str` — this **shadows** the built-in, breaking your code silently.

---

## 3️⃣ Example 1 — Foundational

### Goal

Demonstrate variable creation, reassignment, and type behavior.

```python
# Create a variable holding an integer
age = 25

# Check its type
print(type(age))        # <class 'int'>

# Reassign to a string (dynamic typing allows this)
age = "twenty-five"

# Check its type again — it changed
print(type(age))        # <class 'str'>

# Strong typing: this will crash
# print(age + 10)       # TypeError!

# Explicit conversion fixes it
age = 25
print(str(age) + " years old")   # "25 years old"
```

### Step-by-Step Runtime Walkthrough

1. `age = 25` — Python creates an integer object `25` in memory. The name `age` now points to it.
2. `print(type(age))` — Python follows the `age` label, finds the object, checks its type → `int`.
3. `age = "twenty-five"` — Python creates a **new** string object. The `age` label now points to this string. The integer `25` has no more references and will be garbage collected.
4. `print(type(age))` — Now reports `str`.
5. `age + 10` (commented out) — Would crash because Python refuses to add a string and an integer. This is strong typing in action.
6. `age = 25` — Reassigned back to an integer.
7. `str(age)` — Explicitly converts `25` to the string `"25"`. Now `"25" + " years old"` is string concatenation → `"25 years old"`.

### Runtime Visualization

```
Step 1: age = 25
Memory: age ──► [ int: 25 ]

Step 3: age = "twenty-five"
Memory: age ──► [ str: "twenty-five" ]
        [ int: 25 ] ← orphaned, will be garbage collected

Step 6: age = 25
Memory: age ──► [ int: 25 ]
```

### Output

```
<class 'int'>
<class 'str'>
25 years old
```

---

## 4️⃣ Example 2 — Real-World / Interview Level

### Goal

Show naming conventions, constants, and a common interview trap around Python's object identity.

```python
# Constants (convention only — not enforced)
MAX_RETRIES = 3
BASE_URL = "https://api.example.com"

# Good variable names (snake_case)
user_name = "alice"
login_count = 0

# --- Interview Trap: Identity vs Equality ---

a = [1, 2, 3]
b = [1, 2, 3]
c = a

# Equality: do they have the same VALUE?
print(a == b)    # True  — same contents
print(a == c)    # True  — same contents

# Identity: are they the SAME OBJECT in memory?
print(a is b)    # False — two different list objects
print(a is c)    # True  — c points to the exact same object as a

# Proof: mutate through c, see it through a
c.append(4)
print(a)         # [1, 2, 3, 4] — a changed because c IS a
print(b)         # [1, 2, 3]    — b is a separate object, unaffected
```

### Deep Explanation

- **`==` checks equality** — "Do these two things look the same?"
- **`is` checks identity** — "Are these two names pointing to the exact same object in memory?"
- `a = [1, 2, 3]` creates a list object. `b = [1, 2, 3]` creates a **second, separate** list object that happens to contain the same values.
- `c = a` does **not** copy the list. It makes `c` point to the **same** object as `a`. This is called **aliasing**.
- When you `c.append(4)`, you're modifying the one shared list. Since `a` points to the same list, `a` also sees the change.
- This is one of the most common sources of bugs in Python and a **classic interview question**.

**Performance Note:** `is` is faster than `==` because it just compares memory addresses. But you should only use `is` for checking `None` (`if x is None`), never for comparing values.

### Runtime Flow Diagram

```
a = [1, 2, 3]       b = [1, 2, 3]       c = a

Memory:
a ──┐                b ──► [ list: [1,2,3] ]   (separate object)
    ├──► [ list: [1,2,3] ]
c ──┘

After c.append(4):
a ──┐                b ──► [ list: [1,2,3] ]   (unchanged)
    ├──► [ list: [1,2,3,4] ]
c ──┘
```

### Output

```
True
True
False
True
[1, 2, 3, 4]
[1, 2, 3]
```

---
---

# 2. Execution: Running Python & `if __name__ == "__main__":`

---

## 1️⃣ Concept Overview

### Definition

Python code is executed by the **Python interpreter** — a program that reads your `.py` file line by line and runs it. The special variable `__name__` tells your code **how** it's being run: directly, or as an imported module.

### Why It Exists

- When you write a Python file, it might be used in two ways: (1) run directly as a script, or (2) imported by another file to reuse its functions.
- Without `if __name__ == "__main__":`, **all code at the top level runs on import**. This means importing a utility file could accidentally trigger its main logic — printing output, starting servers, or modifying files.

### Mental Model (Intuition First)

Imagine a recipe card. Sometimes you **cook the recipe** (run the script). Other times you just **lend the card** to a friend so they can use one ingredient list (import the module). The `if __name__ == "__main__":` guard says: "Only start cooking if I'm the one in the kitchen. If someone just borrowed my card, don't start cooking."

### Visual Explanation

```
Scenario 1: python my_script.py
   → Python sets __name__ = "__main__"
   → The if block RUNS

Scenario 2: import my_script (from another file)
   → Python sets __name__ = "my_script"
   → The if block is SKIPPED
```

### When To Use It

- **Always** in any script that could also be imported.
- In every file that has a "main" entry point.
- In professional projects, it's considered **mandatory best practice**.

### When NOT To Use It

- In files that are **purely configuration** (e.g., `settings.py` with only variable assignments).
- In `__init__.py` files (package initializers) — these are meant to run on import.

---

## 2️⃣ Syntax & Structure

### Canonical Syntax

```python
def main():
    """Entry point of the program."""
    # Your main logic here
    print("Program is running!")

if __name__ == "__main__":
    main()
```

### Line-by-Line Breakdown

- `def main():` — Defines a function called `main`. This is a **convention**, not a requirement. You could name it anything, but `main` is universally understood.
- `"""Entry point of the program."""` — A **docstring**. Documents what this function does. We'll cover docstrings in detail in the next section.
- `print("Program is running!")` — The actual logic of your program.
- `if __name__ == "__main__":` — This is the **guard**. `__name__` is a special variable Python sets automatically.
  - If you run this file directly: `__name__` is set to the string `"__main__"`.
  - If another file imports this file: `__name__` is set to the **module name** (e.g., `"my_script"`).
- `main()` — Calls the `main` function. Only executes if the guard condition is `True`.

**Common Beginner Mistakes:**

- Forgetting the double underscores: it's `__name__`, not `_name_` or `name`.
- Putting code **outside** the guard — it will run on import.
- Writing `if __name__ == "main":` (missing the double underscores around `main`).

---

## 3️⃣ Example 1 — Foundational

### Goal

Show the difference between running directly and importing.

**File: `greetings.py`**

```python
# This function is available to anyone who imports this file
def greet(name):
    """Return a greeting string."""
    return f"Hello, {name}!"

# This print runs ALWAYS — on import AND on direct execution
print("Module greetings.py was loaded")

# This block runs ONLY when executed directly
if __name__ == "__main__":
    # Direct execution: demonstrate the greet function
    message = greet("Alice")
    print(message)
```

### Step-by-Step Runtime Walkthrough

**Scenario A: `python greetings.py`**

1. Python opens `greetings.py`, sets `__name__ = "__main__"`.
2. `def greet(name):` — Function is defined (not called yet).
3. `print("Module greetings.py was loaded")` — Executes immediately. Prints the message.
4. `if __name__ == "__main__":` — `"__main__" == "__main__"` is `True`. Enters the block.
5. `message = greet("Alice")` — Calls `greet`, returns `"Hello, Alice!"`, stores in `message`.
6. `print(message)` — Prints `"Hello, Alice!"`.

**Scenario B: From another file, `import greetings`**

1. Python opens `greetings.py`, sets `__name__ = "greetings"`.
2. `def greet(name):` — Function is defined.
3. `print("Module greetings.py was loaded")` — **Still executes!** (This is why you should minimize top-level code.)
4. `if __name__ == "__main__":` — `"greetings" == "__main__"` is `False`. Block is **skipped**.

### Output

**Scenario A (direct run):**
```
Module greetings.py was loaded
Hello, Alice!
```

**Scenario B (imported):**
```
Module greetings.py was loaded
```

---

## 4️⃣ Example 2 — Real-World / Interview Level

### Goal

Show a properly structured script with terminal execution.

**File: `calculator.py`**

```python
"""
Calculator module.

Provides basic arithmetic functions.
Can be run directly for a quick demo, or imported for reuse.
"""

def add(a, b):
    """Return the sum of a and b."""
    return a + b

def multiply(a, b):
    """Return the product of a and b."""
    return a * b

def main():
    """Run a quick demonstration of calculator functions."""
    print(f"3 + 5 = {add(3, 5)}")
    print(f"4 * 7 = {multiply(4, 7)}")

if __name__ == "__main__":
    main()
```

### Deep Explanation

- **Module docstring** at the top: describes the entire file's purpose. This appears when someone runs `help(calculator)` after importing.
- **Functions are defined first**, guard block is at the bottom. This is the standard layout for professional Python files:
  1. Module docstring
  2. Imports (none here)
  3. Constants (none here)
  4. Function/class definitions
  5. `main()` function
  6. `if __name__ == "__main__":` guard
- **Why wrap logic in `main()`?** Instead of putting code directly in the `if` block, we define a `main()` function. This makes the code **testable** — another file can call `calculator.main()` explicitly if needed.

**Running from terminal:**

```bash
# Navigate to the directory containing calculator.py
cd /path/to/project

# Run the script
python calculator.py
```

### Output

```
3 + 5 = 8
4 * 7 = 28
```

---
---

# 3. Documentation: Docstrings & Type Hints

---

## 1️⃣ Concept Overview

### Definition

**Docstrings** are special strings (enclosed in triple quotes `"""..."""`) placed as the first statement in a module, class, or function. They describe what the code does. **Type hints** are annotations that indicate what types a function expects and returns.

### Why It Exists

- Code is read far more often than it is written. Docstrings explain **intent** — what the code is supposed to do, not just what it does mechanically.
- Type hints help **editors and tools** catch bugs before you run the code. They also serve as documentation for other developers.
- Without these, every developer must read the entire implementation to understand how to use a function.

### Mental Model (Intuition First)

- **Docstring** = the label on a medicine bottle. It tells you what it's for, how to use it, and what to expect — without needing to analyze the chemical formula.
- **Type hint** = the shape of a puzzle piece. It tells you what fits before you try to jam it in.

### When To Use It

- **Docstrings:** On every public function, class, and module. Skip them only for trivially obvious private helper functions.
- **Type hints:** On function signatures (parameters and return types). Especially valuable in larger codebases and team projects.

### When NOT To Use It

- Don't write docstrings that just repeat the function name: `def add(a, b): """Adds a and b."""` — this adds no value.
- Don't over-annotate local variables with type hints unless it genuinely improves clarity.

---

## 2️⃣ Syntax & Structure

### Canonical Syntax

```python
def function_name(param1: type1, param2: type2 = default) -> return_type:
    """
    Brief one-line summary of what the function does.

    Args:
        param1: Description of param1.
        param2: Description of param2. Defaults to default.

    Returns:
        Description of what is returned.

    Raises:
        ErrorType: When this error occurs.
    """
    # function body
    return result
```

### Line-by-Line Breakdown

- `param1: type1` — **Type hint** for `param1`. This is a hint, not enforcement. Python will **not** raise an error if you pass the wrong type at runtime. Tools like `mypy` will catch it statically.
- `-> return_type` — Indicates what the function returns.
- `"""..."""` — The **docstring**. Must be the very first statement inside the function.
- `Args:` section — Documents each parameter. The Google style (shown here) is one of several conventions. Others include NumPy style and Sphinx style.
- `Returns:` section — Documents the return value.
- `Raises:` section — Documents exceptions the function might raise.

**Common Beginner Mistakes:**

- Putting the docstring **after** a line of code — it won't be recognized as a docstring.
- Using single quotes `'...'` instead of triple quotes — works technically but breaks multi-line docstrings.
- Thinking type hints enforce types at runtime — they don't. They're for documentation and static analysis only.

---

## 3️⃣ Example 1 — Foundational

### Goal

Show a properly documented function with type hints.

```python
def calculate_area(length: float, width: float) -> float:
    """
    Calculate the area of a rectangle.

    Args:
        length: The length of the rectangle in meters.
        width: The width of the rectangle in meters.

    Returns:
        The area in square meters.

    Raises:
        ValueError: If length or width is negative.
    """
    if length < 0 or width < 0:
        raise ValueError("Dimensions must be non-negative")
    return length * width

# Using the function
area = calculate_area(5.0, 3.0)
print(f"Area: {area} sq meters")

# Accessing the docstring programmatically
print(calculate_area.__doc__)
```

### Step-by-Step Runtime Walkthrough

1. `def calculate_area(...)` — Function is defined. The type hints `float` and `-> float` are stored as metadata but **do not affect execution**.
2. `calculate_area(5.0, 3.0)` — Called with two floats.
3. `if length < 0 or width < 0` — `5.0 < 0` is `False`, `3.0 < 0` is `False`. Guard clause passes.
4. `return 5.0 * 3.0` — Returns `15.0`.
5. `area = 15.0` — Stored in variable.
6. `print(f"Area: {area} sq meters")` — Prints the result.
7. `calculate_area.__doc__` — Every function stores its docstring in the `__doc__` attribute. This is what `help()` reads.

### Output

```
Area: 15.0 sq meters

    Calculate the area of a rectangle.

    Args:
        length: The length of the rectangle in meters.
        width: The width of the rectangle in meters.

    Returns:
        The area in square meters.

    Raises:
        ValueError: If length or width is negative.

```

---

## 4️⃣ Example 2 — Real-World / Interview Level

### Goal

Show type hints with complex types and a module-level docstring.

```python
"""
user_utils.py — Utility functions for user data processing.

This module provides helpers for formatting and validating
user records from the database.
"""

from typing import List, Dict, Optional

def format_user_record(
    name: str,
    age: int,
    email: Optional[str] = None,
    tags: List[str] = None
) -> Dict[str, object]:
    """
    Format a raw user record into a standardized dictionary.

    Args:
        name: The user's full name.
        age: The user's age in years.
        email: The user's email address. None if not provided.
        tags: A list of tags associated with the user.

    Returns:
        A dictionary with keys 'name', 'age', 'email', 'tags'.
    """
    # Guard against mutable default argument (explained in Mutability section)
    if tags is None:
        tags = []

    return {
        "name": name.strip().title(),   # Remove whitespace, capitalize
        "age": age,
        "email": email,
        "tags": tags
    }

# Usage
user = format_user_record("  alice smith  ", 30, email="alice@example.com", tags=["admin"])
print(user)
```

### Deep Explanation

- `from typing import List, Dict, Optional` — These are **type hint helpers** from the standard library. `Optional[str]` means "either `str` or `None`". `List[str]` means "a list containing strings". In Python 3.9+, you can use `list[str]` and `dict[str, object]` directly without importing.
- `tags: List[str] = None` — The default is `None`, **not** an empty list `[]`. This is intentional — we'll explain why in the Mutability Pitfalls section. Short version: using `[]` as a default is a notorious Python bug.
- `name.strip().title()` — **Method chaining**. `.strip()` removes leading/trailing whitespace, `.title()` capitalizes the first letter of each word. `"  alice smith  "` → `"alice smith"` → `"Alice Smith"`.
- The return type `Dict[str, object]` means a dictionary with string keys and values of any type.

### Output

```
{'name': 'Alice Smith', 'age': 30, 'email': 'alice@example.com', 'tags': ['admin']}
```

---
---

# 4. Project Structure: Functions, Modules & Packages

---

## 1️⃣ Concept Overview

### Definition

- A **function** is a reusable block of code that performs a specific task.
- A **module** is a single `.py` file containing functions, classes, and variables.
- A **package** is a directory containing multiple modules and an `__init__.py` file.

### Why It Exists

Without structure, all code lives in one giant file. This makes it:
- Impossible to navigate
- Impossible to test individual pieces
- Impossible for teams to work simultaneously
- Impossible to reuse code across projects

### Mental Model (Intuition First)

```
Function  = a single tool (hammer, screwdriver)
Module    = a toolbox (contains related tools)
Package   = a workshop (contains multiple toolboxes)
```

### Visual Explanation

```
my_project/                  ← Project root
├── main.py                  ← Entry point
├── config.py                ← Constants and settings
├── utils/                   ← Package (directory with __init__.py)
│   ├── __init__.py          ← Makes this directory a package
│   ├── string_helpers.py    ← Module
│   └── math_helpers.py      ← Module
└── tests/                   ← Test package
    ├── __init__.py
    └── test_math_helpers.py
```

### When To Use It

- **Functions:** Whenever you find yourself copying and pasting code, or when a block of code does one distinct thing.
- **Modules:** When a file grows beyond ~200-300 lines, or when functions naturally group by topic.
- **Packages:** When you have multiple related modules.

### When NOT To Use It

- Don't create a function for something you'll only do once in a 10-line script.
- Don't create a package for a project with only one or two files.
- **Never use global variables** to share state between functions — pass data as arguments and return values instead.

---

## 2️⃣ Syntax & Structure

### Canonical Syntax

```python
# --- math_helpers.py (a module) ---

"""Math helper functions for the project."""

# Constants at the top
PI = 3.14159

# Functions below constants
def circle_area(radius: float) -> float:
    """Calculate the area of a circle."""
    return PI * radius ** 2

# Guard block at the bottom
if __name__ == "__main__":
    print(circle_area(5))
```

```python
# --- main.py (importing the module) ---

# Import specific function from a module inside a package
from utils.math_helpers import circle_area

# Or import the whole module
from utils import math_helpers

def main():
    print(circle_area(10))
    print(math_helpers.circle_area(10))  # Same thing, different syntax

if __name__ == "__main__":
    main()
```

### Line-by-Line Breakdown

- `from utils.math_helpers import circle_area` — Reaches into the `utils` package, opens the `math_helpers` module, and pulls out just the `circle_area` function. You can now use it directly by name.
- `from utils import math_helpers` — Imports the entire module. You must prefix with `math_helpers.` when calling functions.
- **Why avoid global variables?** If `main.py` and `math_helpers.py` both modify a global variable, the order of execution becomes unpredictable. Passing data through function arguments makes the flow explicit and testable.

---

## 3️⃣ Example 1 — Foundational

### Goal

Show how to extract repeated logic into a function.

```python
# BAD: Repeated code (Don't do this)
print("Hello, Alice! Welcome.")
print("Hello, Bob! Welcome.")
print("Hello, Charlie! Welcome.")

# GOOD: Use a function
def greet(name: str) -> None:
    """Print a welcome greeting for the given name."""
    print(f"Hello, {name}! Welcome.")

# Call it for each person
greet("Alice")
greet("Bob")
greet("Charlie")
```

### Step-by-Step Runtime Walkthrough

1. `def greet(name):` — Function is defined and stored in memory. Not executed yet.
2. `greet("Alice")` — Python jumps into the function. `name` is assigned `"Alice"` in a new **stack frame**. Prints `"Hello, Alice! Welcome."`. Stack frame is destroyed.
3. `greet("Bob")` — New stack frame. `name` = `"Bob"`. Prints. Frame destroyed.
4. `greet("Charlie")` — Same process.

### Runtime Visualization

```
Call: greet("Alice")
Stack:
+------------------+
| name = "Alice"   |  ← greet's stack frame
+------------------+
| (global scope)   |
+------------------+
Output: Hello, Alice! Welcome.
→ greet's frame is popped off the stack

Call: greet("Bob")
Stack:
+------------------+
| name = "Bob"     |  ← new frame
+------------------+
| (global scope)   |
+------------------+
Output: Hello, Bob! Welcome.
→ frame popped
```

### Output

```
Hello, Alice! Welcome.
Hello, Bob! Welcome.
Hello, Charlie! Welcome.
```

---
---

# 5. Numeric & String Types

---

## 1️⃣ Concept Overview

### Definition

**Numeric types** (`int`, `float`) represent numbers. **Strings** (`str`) represent text. **Casting** is the explicit conversion between types. **f-strings** are Python's modern way to embed expressions inside strings.

### Why It Exists

Programs constantly need to convert between types — reading user input (always a string), performing calculations (need numbers), and displaying results (need strings again).

### Mental Model (Intuition First)

```
User types "42"  →  str: "42"  →  int("42")  →  int: 42  →  42 * 2  →  84  →  str(84)  →  "84"  →  Display
```

Data flows through your program, changing type as needed. You are the traffic controller deciding when to convert.

### When To Use It

- **`int()`**: When you need whole numbers (counting, indexing).
- **`float()`**: When you need decimal precision (measurements, money — though use `Decimal` for money).
- **f-strings**: Whenever you need to build a string that includes variable values. Preferred over `+` concatenation and `.format()`.

### Comparison With Similar Concepts

| Method              | Syntax                        | Readability | Speed   |
|---------------------|-------------------------------|-------------|---------|
| f-string (best)     | `f"Hello, {name}!"`          | ★★★★★       | Fastest |
| `.format()`         | `"Hello, {}!".format(name)`  | ★★★☆☆       | Medium  |
| `%` formatting      | `"Hello, %s!" % name`        | ★★☆☆☆       | Medium  |
| `+` concatenation   | `"Hello, " + name + "!"`     | ★☆☆☆☆       | Slowest |

---

## 2️⃣ Syntax & Structure

### Canonical Syntax

```python
# Casting
x = int("42")          # String to integer
y = float("3.14")      # String to float
z = str(100)           # Integer to string

# f-string formatting
name = "Alice"
age = 30
print(f"Name: {name}, Age: {age}")

# f-string with expressions
print(f"In 5 years: {age + 5}")
print(f"Pi to 2 decimals: {3.14159:.2f}")

# Useful string methods
text = "  Hello, World!  "
print(text.strip())          # "Hello, World!"     — remove whitespace
print(text.split(","))       # ['  Hello', ' World!  '] — split into list
print("-".join(["a", "b"]))  # "a-b"               — join list into string
```

### Line-by-Line Breakdown

- `int("42")` — Parses the string `"42"` and creates integer `42`. Crashes with `ValueError` if the string isn't a valid integer (e.g., `int("hello")`).
- `float("3.14")` — Same idea, but creates a floating-point number.
- `str(100)` — Converts integer `100` to string `"100"`.
- `f"Name: {name}"` — The `f` prefix makes this an **f-string**. Anything inside `{}` is evaluated as a Python expression.
- `{age + 5}` — You can put **any expression** inside the braces, not just variable names.
- `{3.14159:.2f}` — **Format specifier**. `.2f` means "2 decimal places, fixed-point notation". Result: `"3.14"`.
- `.strip()` — Removes leading and trailing whitespace (spaces, tabs, newlines).
- `.split(",")` — Splits the string at every comma, returning a **list** of substrings.
- `"-".join(["a", "b"])` — The **opposite** of split. Takes a list and glues elements together with `"-"` between them.

---

## 3️⃣ Example 1 — Foundational

### Goal

Demonstrate type casting and f-string formatting.

```python
# Simulating user input (input() always returns a string)
user_input = "25"

# Must cast to int before doing math
age = int(user_input)
future_age = age + 10

# Display with f-string
print(f"You are {age}. In 10 years you'll be {future_age}.")

# Formatting numbers
price = 19.99
tax_rate = 0.08
total = price * (1 + tax_rate)
print(f"Total: ${total:.2f}")   # 2 decimal places
```

### Step-by-Step Runtime Walkthrough

1. `user_input = "25"` — A string, not a number.
2. `age = int(user_input)` — Converts `"25"` → `25` (integer).
3. `future_age = 25 + 10` → `35`.
4. `f"You are {age}..."` — Python evaluates `age` (25) and `future_age` (35), inserts them into the string.
5. `total = 19.99 * 1.08` → `21.5892`.
6. `f"${total:.2f}"` — Formats `21.5892` as `"21.59"` (rounded to 2 decimal places).

### Output

```
You are 25. In 10 years you'll be 35.
Total: $21.59
```

---

## 4️⃣ Example 2 — Real-World / Interview Level

### Goal

Show string methods in a data-cleaning scenario.

```python
# Raw CSV-like data (common in real projects)
raw_data = "  Alice, 30, alice@email.com  \n  Bob, 25, bob@email.com  "

# Process each line
lines = raw_data.strip().split("\n")   # Remove outer whitespace, split by newline

for line in lines:
    # Split each line by comma, strip each field
    fields = [field.strip() for field in line.split(",")]

    name = fields[0]
    age = int(fields[1])          # Cast string to int
    email = fields[2]

    # Build a formatted report
    print(f"Name: {name:<10} | Age: {age:>3} | Email: {email}")
```

### Deep Explanation

- `raw_data.strip()` — Removes the leading spaces and trailing spaces/newline from the entire string.
- `.split("\n")` — Splits into two lines: `"  Alice, 30, alice@email.com  "` and `"  Bob, 25, bob@email.com  "`.
- `[field.strip() for field in line.split(",")]` — This is a **list comprehension** (covered later). It splits by comma, then strips whitespace from each piece.
- `{name:<10}` — **Left-align** the name in a 10-character-wide field. Pads with spaces on the right.
- `{age:>3}` — **Right-align** the age in a 3-character-wide field. Pads with spaces on the left.
- This pattern (read raw text → split → strip → cast → format) is extremely common in data processing.

### Output

```
Name: Alice      | Age:  30 | Email: alice@email.com
Name: Bob        | Age:  25 | Email: bob@email.com
```

---
---

# 6. Collections: Lists, Tuples, Sets & Dictionaries

---

## 1️⃣ Concept Overview

### Definition

Python provides four core collection types:

| Collection   | Ordered | Mutable | Duplicates | Syntax          |
|-------------|---------|---------|------------|-----------------|
| **List**    | ✅ Yes  | ✅ Yes  | ✅ Yes     | `[1, 2, 3]`    |
| **Tuple**   | ✅ Yes  | ❌ No   | ✅ Yes     | `(1, 2, 3)`    |
| **Set**     | ❌ No   | ✅ Yes  | ❌ No      | `{1, 2, 3}`    |
| **Dict**    | ✅ Yes* | ✅ Yes  | Keys: ❌   | `{"a": 1}`     |

*Dicts maintain insertion order since Python 3.7.

### Why It Exists

Different data structures solve different problems efficiently:
- **List**: When order matters and you need to modify the collection.
- **Tuple**: When data should never change (coordinates, database rows, function return values).
- **Set**: When you need uniqueness and fast membership testing.
- **Dict**: When you need to look up values by a key.

### Mental Model (Intuition First)

```
List  = a numbered shopping list (you can add, remove, reorder items)
Tuple = a printed receipt (fixed, can't change it)
Set   = a bag of unique marbles (no duplicates, no order)
Dict  = a phone book (look up a number by name)
```

### Visual Explanation

```
List:   [10, 20, 30, 20]
Index:   0   1   2   3      ← positional access

Tuple:  (10, 20, 30)
Index:   0   1   2          ← positional access, but NO modification

Set:    {10, 20, 30}
         ↑ no index, no order, no duplicates

Dict:   {"name": "Alice", "age": 30}
         key → value    key → value
```

---

## 2️⃣ Syntax & Structure

### Canonical Syntax

```python
# --- Lists ---
fruits = ["apple", "banana", "cherry"]
fruits.append("date")          # Add to end
fruits.insert(1, "blueberry")  # Insert at index 1
removed = fruits.pop()         # Remove and return last item
fruits[0] = "avocado"          # Modify by index

# --- Tuples ---
point = (3, 4)
x, y = point                  # Tuple unpacking
# point[0] = 5                # TypeError! Tuples are immutable

# --- Sets ---
unique_ids = {101, 102, 103}
unique_ids.add(104)            # Add element
unique_ids.discard(101)        # Remove (no error if missing)
print(102 in unique_ids)       # Fast membership test: True

# --- Dictionaries ---
user = {"name": "Alice", "age": 30}
user["email"] = "a@b.com"     # Add new key-value pair
print(user["name"])            # Access by key
print(user.get("phone", "N/A"))  # Safe access with default

# Iterating a dictionary
for key, value in user.items():
    print(f"{key}: {value}")
```

### Line-by-Line Breakdown

**Lists:**
- `fruits.append("date")` — Adds to the **end**. O(1) time complexity.
- `fruits.insert(1, "blueberry")` — Inserts at index 1, shifting everything after it right. O(n) time — slow for large lists.
- `fruits.pop()` — Removes and returns the **last** element. O(1). `pop(0)` removes the first but is O(n).
- `fruits[0] = "avocado"` — Direct index assignment. Lists are **mutable**.

**Tuples:**
- `x, y = point` — **Unpacking**. Assigns `3` to `x` and `4` to `y`. The number of variables must match the tuple length.
- Tuples are **immutable** — once created, you cannot change, add, or remove elements.

**Sets:**
- `102 in unique_ids` — Membership testing is **O(1)** (constant time) for sets, vs **O(n)** for lists. This is the main reason to use sets.
- `.discard()` vs `.remove()`: `discard` silently does nothing if the element is missing; `remove` raises `KeyError`.

**Dictionaries:**
- `user["name"]` — Access by key. Raises `KeyError` if key doesn't exist.
- `user.get("phone", "N/A")` — **Safe access**. Returns `"N/A"` if `"phone"` key is missing. Never crashes.
- `.items()` — Returns key-value pairs as tuples. `.keys()` returns just keys. `.values()` returns just values.

---

## 3️⃣ Example 1 — Foundational

### Goal

Show each collection type in a practical mini-scenario.

```python
# --- List: Track a to-do list ---
todos = ["buy milk", "write code", "exercise"]
todos.append("read book")
print(f"Tasks: {todos}")
print(f"First task: {todos[0]}")

# --- Tuple: Store an immutable coordinate ---
origin = (0, 0)
destination = (10, 20)
print(f"Traveling from {origin} to {destination}")

# --- Set: Find unique words in a sentence ---
sentence = "the cat sat on the mat"
words = sentence.split()          # ['the', 'cat', 'sat', 'on', 'the', 'mat']
unique_words = set(words)         # {'the', 'cat', 'sat', 'on', 'mat'}
print(f"Unique words: {unique_words}")
print(f"Word count: {len(words)}, Unique: {len(unique_words)}")

# --- Dict: Store and look up user info ---
user = {"name": "Alice", "age": 30}
user["city"] = "Portland"
for key, value in user.items():
    print(f"  {key}: {value}")
```

### Output

```
Tasks: ['buy milk', 'write code', 'exercise', 'read book']
First task: buy milk
Traveling from (0, 0) to (10, 20)
Unique words: {'mat', 'on', 'cat', 'the', 'sat'}
Word count: 6, Unique: 5
  name: Alice
  age: 30
  city: Portland
```

*(Note: Set output order may vary — sets are unordered.)*

---

## 4️⃣ Example 2 — Real-World / Interview Level

### Goal

Classic interview problem: count word frequency using a dictionary.

```python
def word_frequency(text: str) -> dict:
    """
    Count the frequency of each word in the given text.

    Args:
        text: Input string of words.

    Returns:
        Dictionary mapping each lowercase word to its count.
    """
    # Normalize: lowercase and split into words
    words = text.lower().split()

    # Build frequency dictionary
    freq = {}
    for word in words:
        # .get(word, 0) returns 0 if word not yet in dict
        freq[word] = freq.get(word, 0) + 1

    return freq

# Test it
sample = "the cat sat on the mat the cat"
result = word_frequency(sample)

# Sort by frequency (descending) for display
for word, count in sorted(result.items(), key=lambda x: x[1], reverse=True):
    print(f"  '{word}': {count}")
```

### Deep Explanation

- `text.lower().split()` — Normalize to lowercase first so "The" and "the" count as the same word.
- `freq.get(word, 0) + 1` — The core pattern for counting. If `word` is already in `freq`, get its current count and add 1. If not, start from 0 and add 1.
- `sorted(result.items(), key=lambda x: x[1], reverse=True)` — Sorts the dictionary items by value (count) in descending order. `lambda x: x[1]` means "use the second element of each tuple (the count) as the sort key."

**Alternative:** Python's `collections.Counter` does this in one line: `Counter(words)`. But interviewers want to see you build it manually to prove you understand the logic.

### Output

```
  'the': 3
  'cat': 2
  'sat': 1
  'on': 1
  'mat': 1
```

---
---

# 7. Mutability Pitfalls

---

## 1️⃣ Concept Overview

### Definition

**Mutability** means an object can be changed after creation. Lists, dicts, and sets are mutable. Strings, tuples, ints, and floats are immutable. **Mutability pitfalls** are bugs caused by accidentally sharing or modifying mutable objects.

### Why It Exists

Mutability is useful — you need to add items to lists, update dictionaries, etc. But it creates two notorious traps:

1. **Modifying a list while iterating over it** — causes skipped elements or crashes.
2. **Mutable default arguments** — a default list/dict is created **once** and shared across all calls.

### Mental Model (Intuition First)

**Trap 1 (Iterating + Modifying):** Imagine reading a book while someone rips out pages. You'll skip content or read the wrong page.

**Trap 2 (Mutable Default):** Imagine a shared whiteboard in a function's office. Every time someone calls the function, they write on the **same** whiteboard instead of getting a fresh one.

### Visual Explanation

```
Mutable Default Argument Bug:

Call 1: append_to()  →  result = [1]     ← looks fine
Call 2: append_to()  →  result = [1, 1]  ← wait, why is 1 already there?
Call 3: append_to()  →  result = [1, 1, 1]  ← it's accumulating!

The default list is created ONCE when the function is defined,
not each time the function is called.
```

---

## 2️⃣ Syntax & Structure

### The Bug

```python
# BUG: Mutable default argument
def append_to(element, target=[]):
    target.append(element)
    return target
```

### The Fix

```python
# FIX: Use None as default, create new list inside
def append_to(element, target=None):
    if target is None:
        target = []
    target.append(element)
    return target
```

### Line-by-Line Breakdown

**The Bug:**
- `target=[]` — This empty list is created **once**, when Python first reads the `def` statement. Every call that doesn't provide `target` shares this **same** list object.

**The Fix:**
- `target=None` — `None` is immutable and safe as a default.
- `if target is None: target = []` — Creates a **fresh** list on every call. This is the standard Python pattern.

---

## 3️⃣ Example 1 — Mutable Default Argument

### Goal

Demonstrate the bug and the fix.

```python
# --- THE BUG ---
def buggy_append(element, target=[]):
    """Buggy: shares the same default list across calls."""
    target.append(element)
    return target

print(buggy_append(1))   # [1]       ← looks correct
print(buggy_append(2))   # [1, 2]    ← BUG! Expected [2]
print(buggy_append(3))   # [1, 2, 3] ← BUG! Expected [3]

# --- THE FIX ---
def fixed_append(element, target=None):
    """Fixed: creates a new list each time if none provided."""
    if target is None:
        target = []
    target.append(element)
    return target

print(fixed_append(1))   # [1]
print(fixed_append(2))   # [2]  ← Correct!
print(fixed_append(3))   # [3]  ← Correct!
```

### Runtime Visualization

```
buggy_append — the default list object:

After def:     target_default ──► [ ]
After call 1:  target_default ──► [ 1 ]
After call 2:  target_default ──► [ 1, 2 ]
After call 3:  target_default ──► [ 1, 2, 3 ]

fixed_append:

Call 1: target=None → target = [] → [1]  (new list, discarded after)
Call 2: target=None → target = [] → [2]  (new list, discarded after)
Call 3: target=None → target = [] → [3]  (new list, discarded after)
```

### Output

```
[1]
[1, 2]
[1, 2, 3]
[1]
[2]
[3]
```

---

## 4️⃣ Example 2 — Modifying a List While Iterating

### Goal

Show the iteration bug and the correct approach.

```python
# --- THE BUG ---
numbers = [1, 2, 3, 4, 5, 6]

# Trying to remove even numbers while iterating
for num in numbers:
    if num % 2 == 0:
        numbers.remove(num)

print(f"Bug result: {numbers}")   # [1, 3, 5, 6] ← 6 was skipped!

# --- THE FIX: Build a new list ---
numbers = [1, 2, 3, 4, 5, 6]

# List comprehension: keep only odd numbers
odds = [num for num in numbers if num % 2 != 0]
print(f"Fix result: {odds}")      # [1, 3, 5]

# --- ALTERNATIVE FIX: Iterate over a copy ---
numbers = [1, 2, 3, 4, 5, 6]
for num in numbers[:]:            # numbers[:] creates a shallow copy
    if num % 2 == 0:
        numbers.remove(num)
print(f"Copy fix: {numbers}")     # [1, 3, 5]
```

### Deep Explanation

**Why does the bug skip 6?**

```
Index: 0  1  2  3  4  5
List:  1  2  3  4  5  6

Step 1: index=0, num=1, odd → skip
Step 2: index=1, num=2, even → remove 2
  List becomes: [1, 3, 4, 5, 6]  (everything shifts left!)
Step 3: index=2, num=4, even → remove 4  (3 was skipped!)
  List becomes: [1, 3, 5, 6]
Step 4: index=3, but list length is 4 → num=6... wait, index 3 is 6
  Actually the iterator sees length=4, index=3 → 6, even → remove
  ... behavior depends on implementation, but elements get skipped.
```

The core problem: removing an element shifts all subsequent elements left, but the iterator's index keeps advancing forward. Elements "slip under" the iterator.

**The fix:** Never modify a list you're iterating over. Either:
1. Build a new list (list comprehension) — **preferred**.
2. Iterate over a copy (`numbers[:]`).

### Output

```
Bug result: [1, 3, 5, 6]
Fix result: [1, 3, 5]
Copy fix: [1, 3, 5]
```

---
---

# 8. Control Flow & Iteration

---

## 1️⃣ Concept Overview

### Definition

**Control flow** determines the order in which statements execute. Python provides `if/elif/else` for branching and `for/while` for looping. **Pythonic iteration** means looping directly over collections rather than using index-based loops.

### Why It Exists

Without control flow, programs would execute every line exactly once, top to bottom. You couldn't make decisions, repeat actions, or respond to data.

### Mental Model (Intuition First)

```
if/elif/else = a fork in the road (choose one path)
for loop     = a conveyor belt (process each item)
while loop   = a guard at the door ("keep going until I say stop")
```

### When To Use It

- **`for` loop**: When you know what you're iterating over (a list, range, file, etc.). This is ~90% of loops in Python.
- **`while` loop**: When you don't know how many iterations you need (waiting for user input, polling a server).
- **Iterate directly**: `for item in collection` — not `for i in range(len(collection))`.

### When NOT To Use It

- Don't use `while` when `for` works — `while` loops are more error-prone (infinite loops).
- Don't use `for i in range(len(my_list))` to access elements — use `for item in my_list` or `for i, item in enumerate(my_list)`.

### Comparison With Similar Concepts

| Pattern                              | Pythonic? | Use When                        |
|--------------------------------------|-----------|---------------------------------|
| `for item in items:`                 | ✅ Yes    | You need each value             |
| `for i, item in enumerate(items):`   | ✅ Yes    | You need index AND value        |
| `for i in range(len(items)):`        | ❌ No     | Almost never — use enumerate    |
| `for key, val in dict.items():`      | ✅ Yes    | Iterating over dictionaries     |

---

## 2️⃣ Syntax & Structure

### Canonical Syntax

```python
# --- Branching ---
if condition:
    # runs if condition is True
elif other_condition:
    # runs if first was False and this is True
else:
    # runs if all above were False

# --- For loop (Pythonic) ---
for item in collection:
    # process item

# --- For loop with index ---
for index, item in enumerate(collection):
    # index is 0, 1, 2, ...
    # item is the actual element

# --- While loop ---
while condition:
    # runs repeatedly until condition is False
```

### Line-by-Line Breakdown

- `if condition:` — `condition` is any expression that evaluates to `True` or `False`. Python considers these **falsy**: `False`, `0`, `0.0`, `""`, `[]`, `{}`, `()`, `None`, `set()`. Everything else is **truthy**.
- `elif` — Short for "else if". You can have as many as you want. Only the **first** matching branch executes.
- `for item in collection:` — Python calls `iter()` on the collection, then calls `next()` repeatedly until the collection is exhausted. You never manage an index manually.
- `enumerate(collection)` — Wraps the collection to yield `(index, item)` tuples. Starts at 0 by default; use `enumerate(collection, start=1)` to start at 1.

---

## 3️⃣ Example 1 — Foundational

### Goal

Show Pythonic iteration vs non-Pythonic.

```python
fruits = ["apple", "banana", "cherry"]

# ❌ Non-Pythonic (C-style loop)
for i in range(len(fruits)):
    print(f"{i}: {fruits[i]}")

print("---")

# ✅ Pythonic (direct iteration with enumerate)
for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")
```

### Step-by-Step Runtime Walkthrough

**Pythonic version:**
1. `enumerate(fruits)` creates an iterator yielding `(0, "apple")`, `(1, "banana")`, `(2, "cherry")`.
2. First iteration: `i = 0`, `fruit = "apple"`. Prints `"0: apple"`.
3. Second iteration: `i = 1`, `fruit = "banana"`. Prints `"1: banana"`.
4. Third iteration: `i = 2`, `fruit = "cherry"`. Prints `"2: cherry"`.
5. Iterator exhausted → loop ends.

### Output

```
0: apple
1: banana
2: cherry
---
0: apple
1: banana
2: cherry
```

---

## 4️⃣ Example 2 — Real-World / Interview Level

### Goal

Process a list of student grades with branching and iteration.

```python
def classify_grades(grades: dict) -> dict:
    """
    Classify students into performance categories.

    Args:
        grades: Dictionary mapping student names to scores.

    Returns:
        Dictionary mapping category names to lists of students.
    """
    categories = {"excellent": [], "good": [], "needs_improvement": []}

    for name, score in grades.items():
        if score >= 90:
            categories["excellent"].append(name)
        elif score >= 70:
            categories["good"].append(name)
        else:
            categories["needs_improvement"].append(name)

    return categories

# Test data
student_grades = {
    "Alice": 95,
    "Bob": 72,
    "Charlie": 88,
    "Diana": 65,
    "Eve": 91
}

result = classify_grades(student_grades)

for category, students in result.items():
    print(f"{category}: {', '.join(students) if students else 'none'}")
```

### Deep Explanation

- `grades.items()` — Yields `(name, score)` tuples from the dictionary.
- The `if/elif/else` chain is evaluated **top-down**. A score of 95 matches `>= 90` first, so it never checks `>= 70`.
- `', '.join(students)` — Joins the list of names with `", "`. If the list is empty, `join` returns `""`, so we use the conditional expression `if students else 'none'` to handle that case.
- This pattern (iterate → classify → collect) is extremely common in data processing.

### Runtime Flow Diagram

```
For each (name, score) in grades:
    ↓
    score >= 90? ──Yes──► excellent
    ↓ No
    score >= 70? ──Yes──► good
    ↓ No
    ──────────────────► needs_improvement
```

### Output

```
excellent: Alice, Eve
good: Bob, Charlie
needs_improvement: Diana
```

---
---

# 9. Functions: Arguments & Parameters

---

## 1️⃣ Concept Overview

### Definition

A **parameter** is a variable in a function definition. An **argument** is the actual value passed when calling the function. Python supports **positional arguments** (matched by position), **keyword arguments** (matched by name), and **default parameter values**.

### Why It Exists

Functions need flexible ways to accept input. Sometimes you want to require certain values, sometimes you want to provide sensible defaults, and sometimes you want callers to be explicit about what they're passing.

### Mental Model (Intuition First)

```
def make_coffee(size, sugar=1, milk=True):

Positional:  make_coffee("large")           → size="large", sugar=1, milk=True
Keyword:     make_coffee("large", milk=False) → size="large", sugar=1, milk=False
```

Think of it like ordering coffee: you **must** say the size (positional), but sugar and milk have defaults you can override by name.

### Visual Explanation

```
def func(a, b, c=10, d=20):
         ↑  ↑   ↑       ↑
         |  |   |       └── keyword with default
         |  |   └────────── keyword with default
         |  └────────────── positional (required)
         └───────────────── positional (required)

func(1, 2)            → a=1, b=2, c=10, d=20
func(1, 2, 30)        → a=1, b=2, c=30, d=20
func(1, 2, d=99)      → a=1, b=2, c=10, d=99
```

### When To Use It

- **Positional**: For the 1-2 most important, always-required parameters.
- **Keyword with defaults**: For optional configuration. Makes function calls readable.
- **Keyword-only** (after `*`): When you want to force callers to be explicit.

### When NOT To Use It

- Don't use mutable defaults (`def f(x=[])`) — covered in Mutability Pitfalls.
- Don't have more than ~5 parameters — consider using a dictionary or a class instead.

---

## 2️⃣ Syntax & Structure

### Canonical Syntax

```python
def function_name(
    positional_required,           # Must be provided
    keyword_with_default="value",  # Optional, has a default
    *args,                         # Captures extra positional args as a tuple
    **kwargs                       # Captures extra keyword args as a dict
) -> return_type:
    """Docstring."""
    pass
```

### Line-by-Line Breakdown

- Parameters **without defaults** must come **before** parameters with defaults.
- `*args` — Collects any extra positional arguments into a tuple. Useful for functions that accept a variable number of inputs.
- `**kwargs` — Collects any extra keyword arguments into a dictionary. Useful for passing configuration options through.
- The order must be: positional → `*args` → keyword-only → `**kwargs`.

**Common Beginner Mistakes:**

- Putting a parameter with a default before one without: `def f(a=1, b):` → `SyntaxError`.
- Confusing parameters (in the definition) with arguments (in the call).
- Not realizing that default values are evaluated **once** at function definition time (see Mutability Pitfalls).

---

## 3️⃣ Example 1 — Foundational

### Goal

Show positional, keyword, and default arguments.

```python
def create_profile(name: str, age: int, city: str = "Unknown") -> dict:
    """
    Create a user profile dictionary.

    Args:
        name: User's name (required).
        age: User's age (required).
        city: User's city. Defaults to "Unknown".

    Returns:
        A dictionary with the user's profile.
    """
    return {"name": name, "age": age, "city": city}

# Positional arguments only
print(create_profile("Alice", 30))

# Mix of positional and keyword
print(create_profile("Bob", 25, city="Portland"))

# All keyword (order doesn't matter for keyword args)
print(create_profile(age=40, name="Charlie", city="Seattle"))
```

### Step-by-Step Runtime Walkthrough

1. `create_profile("Alice", 30)` — `name="Alice"` (positional), `age=30` (positional), `city="Unknown"` (default used).
2. `create_profile("Bob", 25, city="Portland")` — `name="Bob"`, `age=25` (positional), `city="Portland"` (keyword overrides default).
3. `create_profile(age=40, name="Charlie", city="Seattle")` — All keyword. Order doesn't matter because Python matches by name.

### Output

```
{'name': 'Alice', 'age': 30, 'city': 'Unknown'}
{'name': 'Bob', 'age': 25, 'city': 'Portland'}
{'name': 'Charlie', 'age': 40, 'city': 'Seattle'}
```

---

## 4️⃣ Example 2 — Real-World / Interview Level

### Goal

Show `*args`, `**kwargs`, and keyword-only arguments.

```python
def log_event(message: str, *tags, level: str = "INFO", **metadata):
    """
    Log an event with optional tags and metadata.

    Args:
        message: The log message (required, positional).
        *tags: Any number of string tags (positional, collected into tuple).
        level: Log level (keyword-only, because it comes after *tags).
        **metadata: Any additional key-value pairs.
    """
    print(f"[{level}] {message}")
    if tags:
        print(f"  Tags: {', '.join(tags)}")
    if metadata:
        for key, value in metadata.items():
            print(f"  {key}: {value}")
    print()

# Basic call
log_event("Server started")

# With tags (extra positional args)
log_event("User login", "auth", "security")

# With level (keyword-only) and metadata (extra keyword args)
log_event("Payment processed", "billing", level="WARNING", amount=99.99, user="Alice")
```

### Deep Explanation

- `*tags` captures `"auth"` and `"security"` into the tuple `("auth", "security")`.
- `level` comes **after** `*tags`, making it **keyword-only**. You cannot pass it positionally — `log_event("msg", "tag", "WARNING")` would put `"WARNING"` into `tags`, not `level`.
- `**metadata` captures `amount=99.99` and `user="Alice"` into the dict `{"amount": 99.99, "user": "Alice"}`.
- This pattern is used extensively in Python libraries (Django views, Flask routes, logging frameworks).

### Runtime Flow Diagram

```
log_event("Payment processed", "billing", level="WARNING", amount=99.99, user="Alice")

Parameter Resolution:
  message  = "Payment processed"     ← first positional
  *tags    = ("billing",)            ← remaining positional args
  level    = "WARNING"               ← keyword-only
  **metadata = {"amount": 99.99, "user": "Alice"}  ← remaining keyword args
```

### Output

```
[INFO] Server started

[INFO] User login
  Tags: auth, security

[WARNING] Payment processed
  Tags: billing
  amount: 99.99
  user: Alice

```

---
---

# 10. Error Handling: try/except & Raising Exceptions

---

## 1️⃣ Concept Overview

### Definition

**Error handling** is the practice of anticipating and gracefully responding to runtime errors (called **exceptions** in Python). The `try/except` block lets you catch errors. The `raise` statement lets you create errors intentionally.

### Why It Exists

Programs interact with unpredictable things: user input, files, networks, databases. Any of these can fail. Without error handling, a single unexpected input crashes your entire program. With it, you can recover, retry, or fail gracefully with a helpful message.

### Mental Model (Intuition First)

```
try:
    # Walk across a tightrope
except SomeError:
    # If you fall, land on this safety net
else:
    # If you made it across safely, celebrate
finally:
    # Whether you fell or not, clean up the equipment
```

### Visual Explanation

```
try block
    ↓
    Success? ──Yes──► else block ──► finally block ──► Continue
    ↓ No (exception raised)
    except block ──► finally block ──► Continue
```

### When To Use It

- **File operations**: File might not exist.
- **User input**: User might type letters when you expect numbers.
- **Network calls**: Server might be down.
- **Dictionary access**: Key might not exist.

### When NOT To Use It

- Don't use `try/except` to hide bugs. If your code has a logic error, fix the logic — don't catch the exception and ignore it.
- **Never use bare `except:`** — it catches *everything*, including `KeyboardInterrupt` (Ctrl+C) and `SystemExit`, making your program impossible to stop.

### Comparison With Similar Concepts

| Pattern                        | Use When                                    |
|-------------------------------|---------------------------------------------|
| `try/except SpecificError`    | You know what can go wrong                  |
| `try/except Exception`       | Catch-all for unexpected errors (use rarely)|
| Bare `except:` (**AVOID**)   | Never — catches system signals too          |
| `if` guard (LBYL)            | Checking is cheap and reliable              |
| `try/except` (EAFP)          | Checking is expensive or race-prone         |

**LBYL** = "Look Before You Leap" (check first, then act)
**EAFP** = "Easier to Ask Forgiveness than Permission" (try it, handle failure) — Python prefers this style.

---

## 2️⃣ Syntax & Structure

### Canonical Syntax

```python
try:
    # Code that might raise an exception
    risky_operation()
except SpecificError as e:
    # Handle this specific error
    print(f"Caught: {e}")
except (TypeError, ValueError) as e:
    # Handle multiple error types
    print(f"Type or Value error: {e}")
else:
    # Runs ONLY if no exception occurred
    print("Success!")
finally:
    # ALWAYS runs, whether exception occurred or not
    print("Cleanup complete")
```

### Line-by-Line Breakdown

- `try:` — Marks the beginning of the "risky" code block.
- `except SpecificError as e:` — Catches only `SpecificError`. The `as e` binds the exception object to `e` so you can inspect it. **Always catch specific exceptions.**
- `except (TypeError, ValueError):` — Catches either type. Use a tuple for multiple exception types.
- `else:` — Runs only if the `try` block completed **without** any exception. Useful for code that should only run on success.
- `finally:` — Runs **no matter what** — even if an exception was raised and not caught, even if there's a `return` statement. Used for cleanup (closing files, releasing locks).

**Common Beginner Mistakes:**

- Using bare `except:` — catches everything, hides bugs.
- Catching `Exception` too broadly when you know the specific error.
- Putting too much code in the `try` block — you might accidentally catch an error from the wrong line.

---

## 3️⃣ Example 1 — Foundational

### Goal

Handle invalid user input gracefully.

```python
def get_number(prompt: str) -> float:
    """
    Repeatedly ask the user for a number until valid input is given.

    Args:
        prompt: The message to display to the user.

    Returns:
        The validated number as a float.
    """
    while True:
        user_input = input(prompt)
        try:
            # Attempt to convert to float
            number = float(user_input)
        except ValueError:
            # float() raised ValueError — input wasn't a number
            print(f"'{user_input}' is not a valid number. Try again.")
        else:
            # No exception — conversion succeeded
            return number

# Simulated usage (in real code, this would use actual input())
# number = get_number("Enter a number: ")
# print(f"You entered: {number}")

# Demonstration with direct calls
for test_input in ["hello", "3.14"]:
    try:
        result = float(test_input)
        print(f"'{test_input}' → {result}")
    except ValueError:
        print(f"'{test_input}' → Invalid!")
```

### Step-by-Step Runtime Walkthrough

1. `float("hello")` — Python cannot convert `"hello"` to a float. Raises `ValueError`.
2. The `except ValueError` block catches it. Prints the error message.
3. `float("3.14")` — Succeeds. Returns `3.14`.
4. The `else` block (or in the demo, the next line) runs because no exception occurred.

### Output

```
'hello' → Invalid!
'3.14' → 3.14
```

---

## 4️⃣ Example 2 — Real-World / Interview Level

### Goal

Show raising custom exceptions and proper error handling in a data processing function.

```python
class InsufficientFundsError(Exception):
    """Raised when a withdrawal exceeds the account balance."""
    pass

def withdraw(balance: float, amount: float) -> float:
    """
    Withdraw money from an account.

    Args:
        balance: Current account balance.
        amount: Amount to withdraw.

    Returns:
        New balance after withdrawal.

    Raises:
        ValueError: If amount is negative.
        InsufficientFundsError: If amount exceeds balance.
    """
    if amount < 0:
        raise ValueError("Withdrawal amount cannot be negative")
    if amount > balance:
        raise InsufficientFundsError(
            f"Cannot withdraw ${amount:.2f} from balance of ${balance:.2f}"
        )
    return balance - amount

# --- Test various scenarios ---
test_cases = [
    (1000.00, 200.00),    # Normal withdrawal
    (1000.00, 1500.00),   # Insufficient funds
    (1000.00, -50.00),    # Negative amount
]

for balance, amount in test_cases:
    try:
        new_balance = withdraw(balance, amount)
        print(f"Withdrew ${amount:.2f}. New balance: ${new_balance:.2f}")
    except InsufficientFundsError as e:
        print(f"DENIED: {e}")
    except ValueError as e:
        print(f"INVALID: {e}")
```

### Deep Explanation

- `class InsufficientFundsError(Exception):` — Creates a **custom exception** by inheriting from `Exception`. The `pass` means it has no extra behavior — it's just a named error type. Custom exceptions make your code self-documenting.
- `raise ValueError(...)` — Intentionally creates and throws an error. The program immediately jumps to the nearest matching `except` block.
- The `except` blocks are ordered from **most specific** to **least specific**. If you put `except Exception` first, it would catch everything and the specific handlers would never run.
- `as e` — Binds the exception to variable `e` so you can access its message.

**Interview Tip:** Interviewers love asking: "What's the difference between `raise` and `return`?" Answer: `return` sends a value back to the caller normally. `raise` sends an error that **must** be caught, or the program crashes. Use `raise` for truly exceptional situations, not for normal control flow.

### Output

```
Withdrew $200.00. New balance: $800.00
DENIED: Cannot withdraw $1500.00 from balance of $1000.00
INVALID: Withdrawal amount cannot be negative
```

---
---

# 11. File Operations & Context Managers

---

## 1️⃣ Concept Overview

### Definition

**File operations** let your program read from and write to files on disk. A **context manager** (the `with` statement) ensures that resources like files are properly opened and closed, even if an error occurs.

### Why It Exists

Programs need to persist data beyond their runtime — saving results, reading configuration, processing logs. Without proper file handling, you risk:
- **Resource leaks**: Files left open consume system resources.
- **Data corruption**: Crashes during writes can leave files in a broken state.
- **Permission errors**: Files locked by your program can't be used by others.

### Mental Model (Intuition First)

```
Without context manager:
  1. Open the door (open file)
  2. Walk in and do work (read/write)
  3. MUST remember to close the door (close file)
  4. If fire alarm goes off mid-work, door stays open! (exception = resource leak)

With context manager (with statement):
  1. Door opens automatically
  2. Do your work
  3. Door closes automatically when you leave — even during a fire alarm
```

### Visual Explanation

```
with open("file.txt", "r") as f:
    data = f.read()
# f is automatically closed here

Equivalent to:
f = open("file.txt", "r")
try:
    data = f.read()
finally:
    f.close()        ← guaranteed to run
```

### When To Use It

- **Always use `with`** for file operations. There is no good reason not to.
- Use `"r"` mode for reading, `"w"` for writing (overwrites!), `"a"` for appending.

### When NOT To Use It

- Don't use `"w"` mode if you want to keep existing content — it **erases** the file first. Use `"a"` to append.
- Don't read entire large files into memory with `.read()` — iterate line by line instead.

### Comparison With Similar Concepts

| Method                  | Memory Usage | Use When                          |
|------------------------|--------------|-----------------------------------|
| `f.read()`             | Entire file  | Small files (< few MB)            |
| `f.readlines()`        | Entire file  | Need all lines as a list          |
| `for line in f:`       | One line     | Large files (memory efficient)    |
| `f.readline()`         | One line     | Need manual control over reading  |

---

## 2️⃣ Syntax & Structure

### Canonical Syntax

```python
# Reading a file
with open("data.txt", "r") as f:
    content = f.read()          # Entire file as one string

# Reading line by line (memory efficient)
with open("data.txt", "r") as f:
    for line in f:
        print(line.strip())     # .strip() removes trailing newline

# Reading all lines into a list
with open("data.txt", "r") as f:
    lines = f.readlines()       # List of strings, each ending with \n

# Writing to a file (overwrites existing content!)
with open("output.txt", "w") as f:
    f.write("Hello, World!\n")

# Appending to a file
with open("output.txt", "a") as f:
    f.write("Another line\n")
```

### Line-by-Line Breakdown

- `open("data.txt", "r")` — Opens the file. `"r"` = read mode (default). Returns a **file object**.
- `as f` — Binds the file object to the variable `f`.
- `f.read()` — Reads the **entire** file into a single string. Fine for small files, dangerous for large ones (could consume all your RAM).
- `for line in f:` — The file object is an **iterator**. Each iteration yields one line. Only one line is in memory at a time — this is how you process gigabyte-sized files.
- `line.strip()` — Each line includes a trailing `\n` (newline character). `.strip()` removes it.
- `f.readlines()` — Reads all lines into a list. Each element is a string ending with `\n`. Uses as much memory as the entire file.
- `"w"` mode — **Overwrites** the file. If the file exists, its contents are erased before writing. If it doesn't exist, it's created.
- `"a"` mode — **Appends** to the file. Existing content is preserved.

**Common Beginner Mistakes:**

- Forgetting that `"w"` mode erases the file.
- Not stripping newlines when reading lines.
- Using `f.read()` on a 10GB log file (crashes with `MemoryError`).
- Using absolute paths like `"/Users/alice/data.txt"` — use relative paths or `os.path` instead.

---

## 3️⃣ Example 1 — Foundational

### Goal

Write to a file, then read it back.

```python
# Write some data
with open("example.txt", "w") as f:
    f.write("Line 1: Hello\n")
    f.write("Line 2: World\n")
    f.write("Line 3: Python\n")
# File is automatically closed here

# Read it back — line by line (memory efficient)
print("Reading line by line:")
with open("example.txt", "r") as f:
    for line_number, line in enumerate(f, start=1):
        clean_line = line.strip()   # Remove trailing \n
        print(f"  {line_number}: {clean_line}")

# Read all at once
print("\nReading all lines into a list:")
with open("example.txt", "r") as f:
    all_lines = f.readlines()
    print(f"  Got {len(all_lines)} lines")
    print(f"  First line (raw): {repr(all_lines[0])}")  # repr shows \n
```

### Step-by-Step Runtime Walkthrough

1. `open("example.txt", "w")` — Creates (or overwrites) `example.txt`. Returns file object.
2. Three `f.write()` calls — Each writes a string to the file. The `\n` creates a new line.
3. `with` block ends — File is flushed (data written to disk) and closed automatically.
4. `open("example.txt", "r")` — Opens for reading.
5. `enumerate(f, start=1)` — Iterates over lines, providing a counter starting at 1.
6. `line.strip()` — `"Line 1: Hello\n"` becomes `"Line 1: Hello"`.
7. `f.readlines()` — Returns `["Line 1: Hello\n", "Line 2: World\n", "Line 3: Python\n"]`.
8. `repr(all_lines[0])` — Shows the raw string including the `\n`: `"'Line 1: Hello\\n'"`.

### Output

```
Reading line by line:
  1: Line 1: Hello
  2: Line 2: World
  3: Line 3: Python

Reading all lines into a list:
  Got 3 lines
  First line (raw): 'Line 1: Hello\n'
```

---

## 4️⃣ Example 2 — Real-World / Interview Level

### Goal

Process a CSV-like file and use `os.path` for safe path handling.

```python
import os

def process_data_file(filename: str) -> list:
    """
    Read a CSV-like data file and return parsed records.

    Args:
        filename: Path to the data file (relative to script location).

    Returns:
        List of dictionaries, one per data row.

    Raises:
        FileNotFoundError: If the file doesn't exist.
    """
    # Build path relative to the current working directory
    filepath = os.path.join(os.getcwd(), filename)

    if not os.path.exists(filepath):
        raise FileNotFoundError(f"No file found at: {filepath}")

    records = []
    with open(filepath, "r") as f:
        # First line is the header
        header = f.readline().strip().split(",")

        # Remaining lines are data
        for line in f:
            values = line.strip().split(",")
            # Zip header and values into a dictionary
            record = dict(zip(header, values))
            records.append(record)

    return records

# --- Create a sample file for demonstration ---
with open("employees.csv", "w") as f:
    f.write("name,department,salary\n")
    f.write("Alice,Engineering,95000\n")
    f.write("Bob,Marketing,72000\n")
    f.write("Charlie,Engineering,88000\n")

# --- Process it ---
employees = process_data_file("employees.csv")
for emp in employees:
    print(f"  {emp['name']} works in {emp['department']}, earns ${emp['salary']}")
```

### Deep Explanation

- `os.path.join()` — Builds file paths in a **cross-platform** way. On Windows it uses `\`, on Mac/Linux it uses `/`. Never hardcode path separators.
- `os.path.exists()` — Checks if the file exists before trying to open it. This is a "Look Before You Leap" approach — appropriate here because the check is cheap.
- `f.readline()` — Reads just the first line (the header). The file cursor advances, so the `for` loop starts from line 2.
- `zip(header, values)` — Pairs up header names with values: `zip(["name", "dept"], ["Alice", "Eng"])` → `[("name", "Alice"), ("dept", "Eng")]`.
- `dict(zip(...))` — Converts those pairs into a dictionary: `{"name": "Alice", "dept": "Eng"}`.

**Why not use the `csv` module?** For production code, you should. This example shows the underlying mechanics so you understand what `csv.DictReader` does internally.

### Output

```
  Alice works in Engineering, earns $95000
  Bob works in Marketing, earns $72000
  Charlie works in Engineering, earns $88000
```

---
---

# 12. Command Line Interfaces (CLI) with Argparse

---

## 1️⃣ Concept Overview

### Definition

**Argparse** is Python's standard library module for building **command-line interfaces** (CLIs). It lets your script accept arguments and flags from the terminal, validates them, and generates help messages automatically.

### Why It Exists

Without argparse, you'd have to manually parse `sys.argv` (a raw list of strings), validate types, handle missing arguments, and write your own help text. Argparse does all of this for you.

### Mental Model (Intuition First)

```
Terminal command:
  python resize.py photo.jpg --width 800 --quality high

Argparse breaks this into:
  positional arg:  "photo.jpg"     (required, no flag)
  optional arg:    --width 800     (flag + value)
  optional arg:    --quality high  (flag + value)
```

Think of argparse as a **receptionist** at the front desk of your program. It checks that visitors (arguments) have the right credentials before letting them in.

### Visual Explanation

```
User types:
  python script.py input.txt -o output.txt --verbose

Argparse parses into:
+------------------+------------------+
| args.input_file  | "input.txt"      |  ← positional (required)
| args.output       | "output.txt"     |  ← optional with value
| args.verbose      | True             |  ← flag (boolean switch)
+------------------+------------------+
```

### When To Use It

- Any script meant to be run from the terminal.
- Scripts that need configurable behavior without editing code.
- Tools that other developers or automated systems will call.

### When NOT To Use It

- Interactive programs that use `input()` for user interaction.
- Libraries/modules that are only imported, never run directly.
- For very complex CLIs with subcommands, consider `click` or `typer` libraries instead.

---

## 2️⃣ Syntax & Structure

### Canonical Syntax

```python
import argparse

def main():
    # 1. Create the parser
    parser = argparse.ArgumentParser(
        description="A brief description of what this script does."
    )

    # 2. Add positional argument (required, no flag)
    parser.add_argument(
        "input_file",              # Name (no dashes = positional)
        help="Path to the input file"
    )

    # 3. Add optional argument with short and long flags
    parser.add_argument(
        "-o", "--output",          # Short flag, long flag
        default="result.txt",      # Default if not provided
        help="Path to the output file (default: result.txt)"
    )

    # 4. Add boolean flag (store_true)
    parser.add_argument(
        "-v", "--verbose",
        action="store_true",       # No value needed; presence = True
        help="Enable verbose output"
    )

    # 5. Parse the arguments
    args = parser.parse_args()

    # 6. Use the arguments
    print(f"Input: {args.input_file}")
    print(f"Output: {args.output}")
    print(f"Verbose: {args.verbose}")

if __name__ == "__main__":
    main()
```

### Line-by-Line Breakdown

- `ArgumentParser(description=...)` — Creates the parser. The `description` appears in the auto-generated `--help` output.
- `add_argument("input_file")` — **Positional argument**. No dashes means it's required and matched by position. The user types the value directly without a flag.
- `add_argument("-o", "--output")` — **Optional argument**. `-o` is the short form, `--output` is the long form. Either works. The variable name in `args` is derived from the long form: `args.output`.
- `default="result.txt"` — If the user doesn't provide `--output`, it defaults to `"result.txt"`.
- `action="store_true"` — Makes this a **boolean switch**. If the user includes `--verbose`, `args.verbose` is `True`. If omitted, it's `False`. No value is expected after the flag.
- `parse_args()` — Reads `sys.argv`, matches arguments to the defined parameters, validates types, and returns a `Namespace` object. If required arguments are missing, it prints an error and exits.

**Common Beginner Mistakes:**

- Forgetting that positional arguments have no dashes.
- Using `store_true` but also expecting a value (`--verbose true` would fail).
- Not wrapping argparse code in `if __name__ == "__main__":` — causes issues when the module is imported.

---

## 3️⃣ Example 1 — Foundational

### Goal

Build a minimal CLI tool that greets a user.

```python
import argparse

def main():
    """A simple greeting CLI tool."""
    parser = argparse.ArgumentParser(description="Greet a user by name.")

    # Positional: the name to greet (required)
    parser.add_argument("name", help="The name of the person to greet")

    # Optional: make the greeting uppercase
    parser.add_argument(
        "-u", "--upper",
        action="store_true",
        help="Print the greeting in uppercase"
    )

    args = parser.parse_args()

    # Build the greeting
    greeting = f"Hello, {args.name}!"

    # Apply the flag
    if args.upper:
        greeting = greeting.upper()

    print(greeting)

if __name__ == "__main__":
    main()
```

### Step-by-Step Runtime Walkthrough

**Command: `python greet.py Alice`**

1. `ArgumentParser` is created.
2. Two arguments are defined: `name` (positional) and `--upper` (flag).
3. `parse_args()` reads `sys.argv` → `["greet.py", "Alice"]`.
4. `args.name = "Alice"`, `args.upper = False` (flag not provided).
5. `greeting = "Hello, Alice!"`.
6. `args.upper` is `False` → skip uppercase.
7. Prints `"Hello, Alice!"`.

**Command: `python greet.py Alice --upper`**

1-3. Same as above, but `sys.argv` → `["greet.py", "Alice", "--upper"]`.
4. `args.name = "Alice"`, `args.upper = True`.
5. `greeting = "Hello, Alice!"`.
6. `args.upper` is `True` → `greeting = "HELLO, ALICE!"`.
7. Prints `"HELLO, ALICE!"`.

**Command: `python greet.py --help`**

Argparse auto-generates:

### Output

```
# python greet.py Alice
Hello, Alice!

# python greet.py Alice --upper
HELLO, ALICE!

# python greet.py --help
usage: greet.py [-h] [-u] name

Greet a user by name.

positional arguments:
  name         The name of the person to greet

optional arguments:
  -h, --help   show this help message and exit
  -u, --upper  Print the greeting in uppercase
```

---

## 4️⃣ Example 2 — Real-World / Interview Level

### Goal

Build a file processing CLI with mutually exclusive groups and type validation.

```python
import argparse
import os

def process_file(filepath: str, mode: str, count: int) -> None:
    """
    Process a file based on the given mode.

    Args:
        filepath: Path to the file.
        mode: Either 'head' (first N lines) or 'tail' (last N lines).
        count: Number of lines to display.
    """
    if not os.path.exists(filepath):
        print(f"Error: File '{filepath}' not found.")
        return

    with open(filepath, "r") as f:
        lines = f.readlines()

    if mode == "head":
        selected = lines[:count]
    else:
        selected = lines[-count:]

    for line in selected:
        print(line.strip())

def main():
    parser = argparse.ArgumentParser(
        description="Display the first or last N lines of a file."
    )

    # Positional: file path (required)
    parser.add_argument("file", help="Path to the file to process")

    # Mutually exclusive: --head or --tail, but not both
    group = parser.add_mutually_exclusive_group(required=True)
    group.add_argument(
        "--head",
        action="store_true",
        help="Show the first N lines"
    )
    group.add_argument(
        "--tail",
        action="store_true",
        help="Show the last N lines"
    )

    # Optional: number of lines (with type validation)
    parser.add_argument(
        "-n", "--count",
        type=int,                  # Argparse converts the string to int
        default=5,
        help="Number of lines to display (default: 5)"
    )

    args = parser.parse_args()

    # Determine mode from mutually exclusive flags
    mode = "head" if args.head else "tail"

    process_file(args.file, mode, args.count)

if __name__ == "__main__":
    main()
```

### Deep Explanation

- `add_mutually_exclusive_group(required=True)` — Creates a group where **only one** flag can be used. If the user passes both `--head` and `--tail`, argparse prints an error and exits. `required=True` means the user **must** pick one.
- `type=int` — Argparse automatically converts the string argument to an integer. If the user passes `--count abc`, argparse prints: `error: argument -n/--count: invalid int value: 'abc'`. You get free type validation.
- `default=5` — If `--count` is not provided, `args.count` is `5`.
- The `mode` variable is derived from which flag was set. Since they're mutually exclusive, exactly one will be `True`.

**Why mutually exclusive groups matter:** Without them, a user could pass `--head --tail` and your program would need to handle the conflict manually. Argparse handles it for you with a clear error message.

### Runtime Flow Diagram

```
Terminal Input
    ↓
ArgumentParser.parse_args()
    ↓
Validate: file exists? ──No──► Error message
    ↓ Yes
Read all lines
    ↓
--head? ──► lines[:count]
--tail? ──► lines[-count:]
    ↓
Print selected lines
```

### Output

```
# Assuming a file "data.txt" with lines 1-10:

# python fileview.py data.txt --head -n 3
Line 1
Line 2
Line 3

# python fileview.py data.txt --tail -n 2
Line 9
Line 10

# python fileview.py data.txt --head --tail
usage: fileview.py [-h] (--head | --tail) [-n COUNT] file
fileview.py: error: argument --tail: not allowed with argument --head
```

---
---

# Quick Reference Card

| Topic                  | Key Takeaway                                                    |
|------------------------|-----------------------------------------------------------------|
| Variables              | Labels pointing to objects; dynamic typing, strong typing       |
| `__name__`             | `"__main__"` when run directly; module name when imported       |
| Docstrings             | Triple-quoted first statement; explains intent, not mechanics   |
| Type Hints             | Annotations for tools/humans; not enforced at runtime           |
| Project Structure      | Functions → Modules → Packages; avoid globals                   |
| Casting                | `int()`, `float()`, `str()` — explicit conversion required      |
| f-strings              | `f"text {expr}"` — fastest, most readable string formatting     |
| Lists                  | Mutable, ordered, indexed — `[1, 2, 3]`                        |
| Tuples                 | Immutable, ordered — `(1, 2, 3)`                                |
| Sets                   | Unique, unordered, O(1) membership — `{1, 2, 3}`               |
| Dicts                  | Key-value pairs — use `.get()` for safe access                  |
| Mutability Pitfalls    | Never mutate while iterating; never use mutable defaults        |
| Pythonic Loops         | `for item in collection`, not `for i in range(len(...))`        |
| `*args` / `**kwargs`  | Capture variable positional/keyword arguments                   |
| `try/except`           | Always catch specific exceptions; never bare `except:`          |
| `with open()`          | Context manager — guarantees file closure                       |
| File Reading           | `for line in f:` for large files; `.read()` for small ones     |
| `os.path`              | Cross-platform path handling; use relative paths                |
| Argparse               | Positional (no dash), optional (with dash), `store_true` flags  |
| Mutually Exclusive     | `add_mutually_exclusive_group()` — prevents conflicting flags   |
