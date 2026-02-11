# Week 2 — Python Intermediate Concepts

---

## Table of Contents

1. [Recursion vs. Iteration](#1-recursion-vs-iteration)
   - [Concept Overview](#concept-overview)
   - [Syntax & Structure](#2️⃣-syntax--structure)
   - [Example 1 — Foundational: Factorial](#3️⃣-example-1--foundational-factorial)
   - [Example 2 — Real-World: Flattening Nested Lists](#4️⃣-example-2--real-world-flattening-nested-lists)
2. [Python Program Architecture](#2-python-program-architecture)
   - [Concept Overview](#concept-overview-1)
   - [Syntax & Structure](#2️⃣-syntax--structure-1)
   - [Example 1 — Foundational: Script vs. Library](#3️⃣-example-1--foundational-script-vs-library)
   - [Example 2 — Real-World: Bytecode & __pycache__](#4️⃣-example-2--real-world-bytecode--pycache)
3. [Imports & Namespaces](#3-imports--namespaces)
   - [Concept Overview](#concept-overview-2)
   - [Syntax & Structure](#2️⃣-syntax--structure-2)
   - [Example 1 — Foundational: Import Methods](#3️⃣-example-1--foundational-import-methods)
   - [Example 2 — Real-World: Namespace Collisions](#4️⃣-example-2--real-world-namespace-collisions)
4. [Packages](#4-packages)
   - [Concept Overview](#concept-overview-3)
   - [Syntax & Structure](#2️⃣-syntax--structure-3)
   - [Example 1 — Foundational: Building a Package](#3️⃣-example-1--foundational-building-a-package)
   - [Example 2 — Real-World: Relative Imports & __init__.py](#4️⃣-example-2--real-world-relative-imports--initpy)

---

# 1. Recursion vs. Iteration

---

## Concept Overview

### Definition

**Recursion** is when a function calls *itself* to solve a smaller version of the same problem, until it reaches a trivial case it can answer directly.

**Iteration** is when you use a loop (`for`, `while`) to repeat a block of code until a condition is met.

Both achieve repetition. They differ in *how* they track progress.

### Why It Exists

Some problems have a naturally **self-similar** structure — the solution to the whole problem depends on solving smaller copies of itself.

- A folder can contain folders, which contain folders…
- A sentence can be parsed into parts that are themselves parseable…
- A mathematical formula like `5!` is defined as `5 × 4!`

Without recursion, you would need to manually simulate the "remember where I was" behavior using your own stack data structure. Recursion lets the language do that bookkeeping for you.

Without iteration, simple counting tasks would require function calls for every step — wasteful and confusing.

### Mental Model (Intuition First)

**Recursion** is like Russian nesting dolls (matryoshka). You open a doll, find a smaller doll inside, open that one, find a smaller one… until you reach the tiniest doll (the **base case**). Then you close them back up in reverse order.

**Iteration** is like walking up stairs. You take one step, then the next, then the next. You keep a counter in your head: "I'm on step 5 of 10."

### Visual Explanation

**Recursion — Call Stack Growth:**

```
factorial(4) is called
  → calls factorial(3)
    → calls factorial(2)
      → calls factorial(1)
        → BASE CASE: returns 1
      ← returns 2 × 1 = 2
    ← returns 3 × 2 = 6
  ← returns 4 × 6 = 24

Call Stack at deepest point:
+------------------+
| factorial(1)     |  ← top (current)
+------------------+
| factorial(2)     |
+------------------+
| factorial(3)     |
+------------------+
| factorial(4)     |  ← bottom (first call)
+------------------+
```

**Iteration — Single Frame, Changing Variables:**

```
Loop iteration:
+------------------+
| i=1, result=1    |  → result = 1 × 1 = 1
| i=2, result=1    |  → result = 1 × 2 = 2
| i=3, result=2    |  → result = 2 × 3 = 6
| i=4, result=6    |  → result = 6 × 4 = 24
+------------------+
Only ONE stack frame the entire time.
```

### When To Use It

**Use Recursion when:**
- The data structure is naturally nested (trees, nested lists, file systems)
- The depth of nesting is unknown at write-time
- The problem is defined recursively (Fibonacci, factorial, tree traversal)
- Code clarity matters more than raw performance

**Use Iteration when:**
- You are counting, accumulating, or processing a flat sequence
- Performance is critical (no function-call overhead)
- The repetition depth is known or bounded
- You want to avoid stack overflow on large inputs

### When NOT To Use It

**Do NOT use Recursion when:**
- A simple loop would be clearer (e.g., summing a list)
- Input size could exceed Python's default recursion limit (~1000 calls)
- Each recursive call does not meaningfully reduce the problem size

**Do NOT use Iteration when:**
- The data has arbitrary nesting depth and you'd need to simulate a stack anyway
- The recursive version is dramatically clearer and input size is small

### Comparison With Similar Concepts

| Approach   | Tracks State Via       | Best For                        | Risk                    |
|------------|------------------------|---------------------------------|-------------------------|
| Recursion  | Call stack (automatic) | Trees, nested data, divide & conquer | Stack overflow          |
| Iteration  | Loop variable (manual) | Flat sequences, counting        | Complex for nested data |
| Iteration + Explicit Stack | Your own list used as a stack | Deep nesting without recursion limit | More code to write |

---

## 2️⃣ Syntax & Structure

### Canonical Syntax — Recursion

```python
def recursive_function(parameter):
    # 1. BASE CASE — the condition that stops recursion
    if parameter meets_trivial_condition:
        return trivial_value

    # 2. RECURSIVE CASE — call yourself with a SMALLER input
    return some_operation(recursive_function(smaller_parameter))
```

### Mandatory Line-by-Line Breakdown

- **Line 1:** Define a function. It will call itself — that's what makes it recursive.
- **Line 3 (Base Case):** This is **mandatory**. Without it, the function calls itself forever until Python raises `RecursionError`. The base case answers the simplest version of the problem directly.
- **Line 6 (Recursive Case):** The function calls itself with a **smaller** or **simpler** input. Each call must move closer to the base case. If it doesn't, you have infinite recursion.

**Common Beginner Mistake:** Forgetting the base case, or writing a recursive case that doesn't shrink the problem.

**Runtime behavior:** Each call creates a new **stack frame** in memory. Python has a default limit of ~1000 frames. Exceeding it raises `RecursionError: maximum recursion depth exceeded`.

### Canonical Syntax — Iteration

```python
result = initial_value

for item in sequence:       # or: while condition:
    result = update(result, item)

# result now holds the final answer
```

### Mandatory Line-by-Line Breakdown

- **Line 1:** Initialize an accumulator variable *before* the loop.
- **Line 3:** The loop header. `for` iterates over a known sequence. `while` continues until a condition becomes `False`.
- **Line 4:** Update the accumulator each pass. This is where the work happens.
- **Line 6:** After the loop ends, `result` holds the answer.

**Runtime behavior:** Only one stack frame exists. The loop variable and accumulator are updated in-place. No risk of stack overflow.

---

## 3️⃣ Example 1 — Foundational: Factorial

### Goal

Demonstrate the smallest meaningful recursive function and its iterative equivalent.

**Mathematical definition:** `n! = n × (n-1) × (n-2) × … × 1`, and `0! = 1`.

### Recursive Version

```python
# Compute n! using recursion
def factorial_recursive(n):
    # Base case: 0! and 1! are both 1
    if n <= 1:
        return 1

    # Recursive case: n! = n * (n-1)!
    return n * factorial_recursive(n - 1)

# Test it
print(factorial_recursive(5))
```

### Step-by-Step Runtime Walkthrough

1. `factorial_recursive(5)` is called. `n=5`. Not ≤ 1, so we go to the recursive case.
2. Python must evaluate `5 * factorial_recursive(4)`. It **pauses** this call and calls `factorial_recursive(4)`.
3. `n=4`. Not ≤ 1. Pauses, calls `factorial_recursive(3)`.
4. `n=3`. Not ≤ 1. Pauses, calls `factorial_recursive(2)`.
5. `n=2`. Not ≤ 1. Pauses, calls `factorial_recursive(1)`.
6. `n=1`. **Base case hit!** Returns `1`.
7. Now the paused calls **unwind** in reverse:
   - `factorial_recursive(2)` returns `2 * 1 = 2`
   - `factorial_recursive(3)` returns `3 * 2 = 6`
   - `factorial_recursive(4)` returns `4 * 6 = 24`
   - `factorial_recursive(5)` returns `5 * 24 = 120`

### Runtime Visualization

```
Call:   factorial_recursive(5)
Stack:  [f(5)]

Call:   factorial_recursive(4)
Stack:  [f(5), f(4)]

Call:   factorial_recursive(3)
Stack:  [f(5), f(4), f(3)]

Call:   factorial_recursive(2)
Stack:  [f(5), f(4), f(3), f(2)]

Call:   factorial_recursive(1)
Stack:  [f(5), f(4), f(3), f(2), f(1)]
        ↑ BASE CASE — returns 1

Unwind: f(2) = 2*1 = 2
Stack:  [f(5), f(4), f(3), f(2)=2]

Unwind: f(3) = 3*2 = 6
Stack:  [f(5), f(4), f(3)=6]

Unwind: f(4) = 4*6 = 24
Stack:  [f(5), f(4)=24]

Unwind: f(5) = 5*24 = 120
Stack:  [f(5)=120]

Final return: 120
```

### Iterative Version

```python
# Compute n! using iteration
def factorial_iterative(n):
    result = 1                  # Start with the identity for multiplication

    for i in range(2, n + 1):  # Multiply 2, 3, 4, ..., n
        result = result * i

    return result

# Test it
print(factorial_iterative(5))
```

**Walkthrough:**

| Step | `i` | `result` before | Operation     | `result` after |
|------|-----|-----------------|---------------|----------------|
| 1    | 2   | 1               | 1 × 2         | 2              |
| 2    | 3   | 2               | 2 × 3         | 6              |
| 3    | 4   | 6               | 6 × 4         | 24             |
| 4    | 5   | 24              | 24 × 5        | 120            |

### Output (both versions)

```
120
```

---

## 4️⃣ Example 2 — Real-World: Flattening Nested Lists

### Goal

Show where recursion genuinely shines: processing data with **unknown nesting depth**. This is a common interview question and real-world task (e.g., parsing JSON, traversing file trees).

**Problem:** Given `[1, [2, [3, 4], 5], 6]`, produce `[1, 2, 3, 4, 5, 6]`.

### Recursive Solution

```python
def flatten_recursive(data):
    """Flatten a list that may contain nested lists to any depth."""
    result = []

    for item in data:
        if isinstance(item, list):
            # Item is a list — recurse into it
            inner = flatten_recursive(item)
            result.extend(inner)
        else:
            # Item is a plain value — add it directly
            result.append(item)

    return result

# Test
nested = [1, [2, [3, 4], 5], 6]
print(flatten_recursive(nested))
```

### Deep Explanation

- **Why recursion here?** We don't know how deep the nesting goes. It could be `[1, [2, [3, [4, [5]]]]]`. A fixed number of nested loops can't handle arbitrary depth. Recursion naturally handles "go deeper until you can't."
- **Base case is implicit:** When `item` is not a list, we just append it. The recursion only happens when we encounter a list. Eventually, every nested list bottoms out into non-list items.
- **`isinstance(item, list)`** checks if the item is a list. This is the decision point: recurse or collect.
- **`result.extend(inner)`** adds all elements from the flattened sub-list into our result. Using `append` here would re-nest it.

### Runtime Flow Diagram

```
flatten_recursive([1, [2, [3, 4], 5], 6])
│
├─ item=1 → not a list → append 1
│
├─ item=[2, [3, 4], 5] → IS a list → RECURSE
│   │
│   ├─ item=2 → append 2
│   ├─ item=[3, 4] → IS a list → RECURSE
│   │   ├─ item=3 → append 3
│   │   └─ item=4 → append 4
│   │   └─ returns [3, 4]
│   ├─ extend result with [3, 4]
│   ├─ item=5 → append 5
│   └─ returns [2, 3, 4, 5]
│
├─ extend result with [2, 3, 4, 5]
│
├─ item=6 → not a list → append 6
│
└─ returns [1, 2, 3, 4, 5, 6]
```

### Iterative Solution (Using Explicit Stack)

```python
def flatten_iterative(data):
    """Flatten nested lists using an explicit stack instead of recursion."""
    # We use our own stack to simulate what recursion does automatically
    stack = [data]          # Start with the entire input
    result = []

    while stack:
        current = stack.pop()

        if isinstance(current, list):
            # Push items in REVERSE order so they come off in correct order
            for item in reversed(current):
                stack.append(item)
        else:
            result.append(current)

    return result

# Test
nested = [1, [2, [3, 4], 5], 6]
print(flatten_iterative(nested))
```

**Why reversed?** A stack is Last-In-First-Out (LIFO). If we push `[1, 2, 3]` in order, we'd pop `3` first. Reversing ensures `1` is on top and comes out first.

**Trade-off:** This version avoids the recursion limit but requires more mental effort to understand. In interviews, mention both approaches and explain the trade-off.

### Output (both versions)

```
[1, 2, 3, 4, 5, 6]
```

---
---

# 2. Python Program Architecture

---

## Concept Overview

### Definition

**Python Program Architecture** refers to how Python files are organized and executed — specifically, the distinction between **scripts** (files you run directly) and **libraries/modules** (files you import into other files).

The key mechanism is the `if __name__ == '__main__':` guard, which lets a single file serve both roles.

### Why It Exists

Without this architecture:
- Every time you imported a helper file, all its top-level code would execute (printing output, running tests, launching programs you didn't ask for).
- You couldn't reuse functions from a file without also triggering its "main" behavior.
- Large programs would be impossible to organize cleanly.

### Mental Model (Intuition First)

Think of a Python file like a **Swiss Army knife**:
- When you **open it directly** (run it as a script), the main blade comes out — it does its primary job.
- When **another tool borrows it** (imports it), only the specific tool (function) requested is used — the main blade stays folded.

The `if __name__ == '__main__':` block is the **lock mechanism** that decides which mode you're in.

### Visual Explanation

```
When you RUN a file directly:
$ python myfile.py

Python sets:  __name__ = "__main__"
                         ↓
         if __name__ == "__main__":   ← TRUE
             main code runs ✓


When another file IMPORTS it:
import myfile

Python sets:  __name__ = "myfile"
                         ↓
         if __name__ == "__main__":   ← FALSE
             main code does NOT run ✗
             (but functions are available)
```

### When To Use It

- **Always** use `if __name__ == '__main__':` in any file that could be both run and imported.
- Use it in every file that has a "do something" section (not just function/class definitions).
- Professional projects use this in virtually every Python file.

### When NOT To Use It

- Pure configuration files (e.g., `settings.py` that only defines constants).
- Files that are *only* ever imported and never run directly (though adding it doesn't hurt).

### Comparison With Similar Concepts

| Concept              | Purpose                              | Analogy                    |
|----------------------|--------------------------------------|----------------------------|
| Script               | Run directly to perform a task       | A program you double-click |
| Module/Library       | Imported to provide reusable code    | A toolbox you borrow from  |
| `__name__` guard     | Let one file be both                 | A dual-mode switch         |
| `__pycache__`/`.pyc` | Cached compiled bytecode for speed   | Pre-cooked meal for faster reheating |

---

## 2️⃣ Syntax & Structure

### Canonical Syntax

```python
# mymodule.py

def useful_function():
    """A function other files can import."""
    return "I'm useful!"

def another_helper():
    """Another importable function."""
    return 42

# --- This block ONLY runs when the file is executed directly ---
if __name__ == '__main__':
    # Script behavior: test, demo, or main program logic
    print(useful_function())
    print(another_helper())
```

### Mandatory Line-by-Line Breakdown

- **Lines 3-8:** Function definitions. These are created when the file is loaded (whether run or imported). They exist in the module's namespace.
- **Line 11:** The guard. Python automatically sets the special variable `__name__`:
  - To `"__main__"` if this file is the one you ran (`python mymodule.py`).
  - To `"mymodule"` if another file did `import mymodule`.
- **Lines 12-14:** Code inside the guard only executes in "script mode." When imported, these lines are skipped entirely.

**Common Beginner Mistake:** Putting important initialization code *outside* the guard. If someone imports your file, that code runs unexpectedly.

**Runtime behavior:**
1. Python reads the file top-to-bottom.
2. Function `def` statements create function objects but don't execute the function body.
3. The `if __name__` check evaluates.
4. If true, the guarded code runs. If false, Python finishes loading the module silently.

---

## 3️⃣ Example 1 — Foundational: Script vs. Library

### Goal

Show the same file behaving differently depending on how it's used.

**File: `greetings.py`**

```python
# greetings.py — A module that can also be run as a script

def greet(name):
    """Return a greeting string for the given name."""
    return f"Hello, {name}! Welcome aboard."

def farewell(name):
    """Return a farewell string."""
    return f"Goodbye, {name}. See you next time!"

# This block runs ONLY when greetings.py is executed directly
if __name__ == '__main__':
    print(greet("Alice"))
    print(farewell("Bob"))
```

### Step-by-Step Runtime Walkthrough

**Scenario A: Run directly**

```bash
$ python greetings.py
```

1. Python opens `greetings.py`.
2. Sets `__name__` to `"__main__"` (because this is the file being run).
3. Reads line 3: creates function object `greet` in memory.
4. Reads line 8: creates function object `farewell` in memory.
5. Reads line 12: evaluates `__name__ == '__main__'` → `"__main__" == "__main__"` → `True`.
6. Executes line 13: calls `greet("Alice")`, prints result.
7. Executes line 14: calls `farewell("Bob")`, prints result.

**Output:**

```
Hello, Alice! Welcome aboard.
Goodbye, Bob. See you next time!
```

**Scenario B: Imported by another file**

```python
# app.py
import greetings

print(greetings.greet("Charlie"))
```

1. Python opens `greetings.py` (triggered by `import greetings`).
2. Sets `__name__` to `"greetings"` (the module name, NOT `"__main__"`).
3. Creates `greet` and `farewell` function objects.
4. Evaluates `__name__ == '__main__'` → `"greetings" == "__main__"` → `False`.
5. **Skips the guarded block entirely.** No printing happens from `greetings.py`.
6. Back in `app.py`, line 3 calls `greetings.greet("Charlie")`.

**Output (from app.py):**

```
Hello, Charlie! Welcome aboard.
```

### Runtime Visualization

```
Scenario A (direct run):
+---------------------------+
| __name__ = "__main__"     |
| greet = <function>        |
| farewell = <function>     |
| → guard is TRUE           |
| → print(greet("Alice"))   |
| → print(farewell("Bob"))  |
+---------------------------+

Scenario B (imported):
+---------------------------+
| __name__ = "greetings"    |
| greet = <function>        |
| farewell = <function>     |
| → guard is FALSE          |
| → (nothing else happens)  |
+---------------------------+
```

---

## 4️⃣ Example 2 — Real-World: Bytecode & `__pycache__`

### Goal

Understand what happens behind the scenes when Python imports a module, and why a `__pycache__` directory appears.

### How Python Compiles and Caches

```python
# math_utils.py
def add(a, b):
    """Add two numbers."""
    return a + b

def multiply(a, b):
    """Multiply two numbers."""
    return a * b

if __name__ == '__main__':
    print(add(3, 4))
```

```python
# main.py
import math_utils   # This triggers compilation

result = math_utils.add(10, 20)
print(result)
```

### Deep Explanation

When you run `python main.py`:

1. Python encounters `import math_utils`.
2. It finds `math_utils.py` in the current directory.
3. Python **compiles** `math_utils.py` into **bytecode** — a lower-level representation that the Python Virtual Machine (PVM) can execute faster than raw source code.
4. This bytecode is saved to disk as `__pycache__/math_utils.cpython-312.pyc` (the number matches your Python version).
5. **Next time** you import `math_utils`, Python checks:
   - Does the `.pyc` file exist?
   - Is it newer than the `.py` source file?
   - If both yes: **skip recompilation**, load the cached bytecode directly. This is faster.
   - If the source changed: recompile and update the cache.

### Runtime Flow Diagram

```
$ python main.py

main.py loads
   ↓
"import math_utils" encountered
   ↓
Python searches for math_utils:
  1. Current directory     ← found: math_utils.py
  2. PYTHONPATH directories
  3. Standard library
   ↓
Compile math_utils.py → bytecode
   ↓
Save to __pycache__/math_utils.cpython-312.pyc
   ↓
Load bytecode into memory
   ↓
Create module object "math_utils" with:
  - math_utils.add
  - math_utils.multiply
   ↓
Continue executing main.py
   ↓
math_utils.add(10, 20) → 30
   ↓
print(30)
```

### File System After First Import

```
project/
├── main.py
├── math_utils.py
└── __pycache__/
    └── math_utils.cpython-312.pyc   ← auto-generated
```

**Key points:**
- You never need to create or manage `__pycache__` — Python handles it automatically.
- `.pyc` files are **not** human-readable. They contain bytecode.
- Deleting `__pycache__` is safe — Python will regenerate it on the next import.
- Files run directly (`python main.py`) are **not** cached. Only imported modules are.
- Add `__pycache__/` to your `.gitignore` — it should not be committed to version control.

### Output

```
30
```

---
---

# 3. Imports & Namespaces

---

## Concept Overview

### Definition

An **import** is how one Python file accesses code defined in another Python file (or a built-in/third-party library).

A **namespace** is an isolated container of names (variables, functions, classes). Each module gets its own namespace, preventing names in one file from colliding with names in another.

### Why It Exists

Without imports, you'd have to copy-paste code between files. Every change would need to be duplicated everywhere.

Without namespaces, if two files both defined a function called `process()`, one would silently overwrite the other. In a large project with hundreds of files, name collisions would be constant and catastrophic.

### Mental Model (Intuition First)

**Imports** are like borrowing a book from a library. You don't rewrite the book — you just reference it.

**Namespaces** are like last names. There can be a "John Smith" and a "John Doe" in the same room without confusion, because the last name (namespace) disambiguates them. Similarly, `math.sqrt` and `cmath.sqrt` are different functions that happen to share a first name.

### Visual Explanation

```
Without namespaces (dangerous):
+----------------------------------+
| Global Space                     |
|   sqrt = math version            |
|   sqrt = cmath version  ← OVERWRITES! |
+----------------------------------+

With namespaces (safe):
+----------------+  +----------------+
| math namespace |  | cmath namespace|
|   sqrt = ...   |  |   sqrt = ...   |
+----------------+  +----------------+
       ↑                    ↑
  math.sqrt            cmath.sqrt
  (real numbers)       (complex numbers)
  No conflict!
```

### When To Use It

- **`import module`** — When you use many things from a module, or want clarity about where each name comes from.
- **`import module as alias`** — When the module name is long (`import numpy as np`).
- **`from module import name`** — When you use one specific thing frequently and want shorter code.

### When NOT To Use It

- **`from module import *`** — Almost never. It dumps all names into your namespace, causing silent collisions and making code unreadable ("where did this function come from?").
- **Circular imports** — If `a.py` imports `b.py` and `b.py` imports `a.py`, you get errors. Restructure your code instead.

### Comparison With Similar Concepts

| Import Style                  | Namespace Clarity | Typing Effort | Collision Risk |
|-------------------------------|-------------------|---------------|----------------|
| `import math`                 | High (`math.sqrt`)| More typing   | None           |
| `import numpy as np`          | High (`np.array`) | Medium        | None           |
| `from math import sqrt`       | Medium (`sqrt`)   | Less typing   | Low            |
| `from math import *`          | None              | Least typing  | **High**       |

---

## 2️⃣ Syntax & Structure

### Canonical Syntax

```python
# Style 1: Full module import
import math

# Style 2: Aliased import
import numpy as np

# Style 3: Specific member import
from math import sqrt, pi

# Style 4: Aliased member import
from collections import OrderedDict as OD

# Style 5: Wildcard import (AVOID)
from math import *
```

### Mandatory Line-by-Line Breakdown

- **Style 1 (`import math`):** Loads the entire `math` module. Access members via `math.sqrt()`, `math.pi`. The name `math` is added to your namespace.
- **Style 2 (`import numpy as np`):** Same as Style 1, but the module is accessible via the shorter alias `np`. The name `numpy` is NOT in your namespace — only `np` is.
- **Style 3 (`from math import sqrt, pi`):** Pulls `sqrt` and `pi` directly into your namespace. You use `sqrt(16)` instead of `math.sqrt(16)`. The name `math` is NOT in your namespace.
- **Style 4 (`from collections import OrderedDict as OD`):** Imports a specific member and renames it.
- **Style 5 (`from math import *`):** Imports every public name from `math` into your namespace. **Dangerous** — you don't know what names were added, and they can overwrite your own variables silently.

**How Python finds modules (the search path):**

```
When you write: import something

Python searches IN THIS ORDER:
1. Current directory (where your script is)
2. Directories in PYTHONPATH environment variable
3. Standard library directories
4. Site-packages (where pip installs third-party packages)

All of these are listed in: sys.path
```

**Runtime behavior:**
1. Python finds the module file.
2. Compiles it to bytecode (if not cached).
3. Executes the module's top-level code (creating functions, classes, variables).
4. Creates a module object.
5. Binds the module object (or specific names) to your namespace.
6. **Subsequent imports of the same module reuse the already-loaded object** — the module code does NOT run again.

---

## 3️⃣ Example 1 — Foundational: Import Methods

### Goal

Show all import styles in action and demonstrate how namespaces keep things organized.

```python
# demo_imports.py — Demonstrating different import styles

# Style 1: Full import
import math
print(math.sqrt(25))       # Access via module.name
print(math.pi)

# Style 2: Aliased import
import datetime as dt
today = dt.date.today()     # Shorter to type
print(today)

# Style 3: Specific import
from os.path import exists, join
print(exists("demo_imports.py"))   # No prefix needed
path = join("folder", "file.txt")
print(path)

# Style 4: Showing namespace isolation
import math
x = 42                      # Our own variable named 'x'
# math also has internal variables, but they don't touch ours
print(f"Our x: {x}")
print(f"math's pi: {math.pi}")
```

### Step-by-Step Runtime Walkthrough

1. `import math` — Python loads the `math` module. The name `math` now exists in our namespace, pointing to the module object.
2. `math.sqrt(25)` — Looks up `sqrt` inside the `math` module's namespace. Calls it with `25`. Returns `5.0`.
3. `math.pi` — Looks up `pi` inside `math`. Returns `3.141592653589793`.
4. `import datetime as dt` — Loads `datetime` module, but binds it to the name `dt` in our namespace.
5. `dt.date.today()` — Accesses the `date` class inside `datetime`, calls its `today()` method.
6. `from os.path import exists, join` — Loads `os.path` module, but only pulls `exists` and `join` into our namespace directly.
7. `exists("demo_imports.py")` — Calls the function directly (no prefix). Returns `True` if the file exists.
8. `x = 42` — Creates our own variable. It lives in our module's namespace, completely separate from anything in `math`.

### Runtime Visualization

```
Our namespace (demo_imports.py):
+---------------------------+
| math → <module 'math'>   |
| dt → <module 'datetime'> |
| exists → <function>      |
| join → <function>        |
| today → 2026-02-10       |
| x → 42                   |
+---------------------------+

math's namespace (separate):
+---------------------------+
| sqrt → <built-in func>   |
| pi → 3.14159...          |
| e → 2.71828...           |
| ... (many more)          |
+---------------------------+

No overlap. No conflict.
```

### Output

```
5.0
3.141592653589793
2026-02-10
True
folder/file.txt
Our x: 42
math's pi: 3.141592653589793
```

---

## 4️⃣ Example 2 — Real-World: Namespace Collisions

### Goal

Demonstrate why `from module import *` is dangerous and how namespaces prevent bugs.

```python
# collision_demo.py — Why wildcard imports are dangerous

# Suppose we define our own function
def sqrt(x):
    """Our custom 'sqrt' that returns an integer result."""
    return int(x ** 0.5)

print(f"Our sqrt(25): {sqrt(25)}")       # Returns 5 (int)
print(f"Our sqrt(2):  {sqrt(2)}")        # Returns 1 (int, truncated)

# Now we do a wildcard import...
from math import *

# Our sqrt is GONE — silently overwritten!
print(f"After import *: sqrt(25) = {sqrt(25)}")   # Returns 5.0 (float)
print(f"After import *: sqrt(2)  = {sqrt(2)}")    # Returns 1.4142... (float)

# We lost our custom function with NO warning.
```

### Deep Explanation

- **Before the wildcard import:** `sqrt` in our namespace points to our custom function that returns integers.
- **`from math import *`** dumps every public name from `math` into our namespace. This includes `sqrt`, `pi`, `e`, `sin`, `cos`, and dozens more.
- **Our `sqrt` is silently overwritten** by `math.sqrt`. Python gives no warning. No error. The old function is simply gone.
- **This is why `import *` is banned in professional code.** You can't tell which names were imported, and you can't predict collisions.

**The safe alternative:**

```python
# Safe: use full import
import math

def sqrt(x):
    return int(x ** 0.5)

print(sqrt(25))        # Our version: 5
print(math.sqrt(25))   # math's version: 5.0
# Both coexist peacefully!
```

### Runtime Flow Diagram

```
Before "from math import *":
+-------------------+
| sqrt → our func   |
+-------------------+

After "from math import *":
+-------------------+
| sqrt → math.sqrt  |  ← OVERWRITTEN
| pi → 3.14159...   |  ← added
| e → 2.71828...    |  ← added
| sin → ...         |  ← added
| cos → ...         |  ← added
| (dozens more)     |
+-------------------+

Our original sqrt is lost forever.
```

### Output

```
Our sqrt(25): 5
Our sqrt(2):  1
After import *: sqrt(25) = 5.0
After import *: sqrt(2)  = 1.4142135623730951
```

---
---

# 4. Packages

---

## Concept Overview

### Definition

A **package** is a directory that contains Python modules (`.py` files) and a special `__init__.py` file. It's a way to organize related modules into a hierarchy, like folders on your computer.

### Why It Exists

As projects grow, you might have dozens or hundreds of modules. Without packages:
- All files would sit in one flat directory — chaos.
- Module names would need to be globally unique — impossible in large projects.
- There would be no logical grouping of related code.

Packages let you create a hierarchy:

```
myproject/
├── database/
│   ├── __init__.py
│   ├── connection.py
│   └── queries.py
├── api/
│   ├── __init__.py
│   ├── routes.py
│   └── auth.py
└── main.py
```

Now `database.connection` and `api.routes` are clearly organized and can't collide.

### Mental Model (Intuition First)

A **module** is a single file (like a single document).
A **package** is a folder of files (like a binder of documents).

The `__init__.py` file is like the **table of contents** at the front of the binder. It tells Python "this folder is a package" and optionally controls what's visible when someone opens the binder.

### Visual Explanation

```
Package Structure:

animals/                    ← Package (directory)
├── __init__.py             ← "Table of contents" — runs on import
├── dog.py                  ← Module
├── cat.py                  ← Module
└── birds/                  ← Sub-package (nested directory)
    ├── __init__.py         ← Sub-package init
    ├── eagle.py            ← Module
    └── penguin.py          ← Module

Import paths:
  import animals            → runs animals/__init__.py
  import animals.dog        → runs animals/__init__.py, then loads dog.py
  from animals.birds import eagle  → loads eagle.py
```

### When To Use It

- Any project with more than a handful of modules.
- When modules naturally group into categories (database, API, utilities, tests).
- When you want to control what's exposed to users of your package.
- When building a library for others to install via `pip`.

### When NOT To Use It

- Tiny scripts with 1-2 files — a package adds unnecessary structure.
- When a single module file is sufficient.

### Comparison With Similar Concepts

| Concept       | What It Is                    | File System Analogy |
|---------------|-------------------------------|---------------------|
| Module        | A single `.py` file           | A document          |
| Package       | A directory with `__init__.py`| A folder            |
| Sub-package   | A package inside a package    | A subfolder         |
| `__init__.py` | Package initializer           | Folder's table of contents |

---

## 2️⃣ Syntax & Structure

### Canonical Syntax — Package Directory

```
mypackage/
├── __init__.py          # Required (can be empty)
├── module_a.py
├── module_b.py
└── subpackage/
    ├── __init__.py      # Required for sub-packages too
    └── module_c.py
```

### `__init__.py` — The Package Initializer

```python
# mypackage/__init__.py

# This code runs when anyone does: import mypackage

# Option 1: Empty file (just marks directory as a package)

# Option 2: Expose specific names for convenience
from .module_a import useful_function
from .module_b import HelperClass

# Option 3: Define __all__ to control "from mypackage import *"
__all__ = ['useful_function', 'HelperClass']
```

### Mandatory Line-by-Line Breakdown

- **`__init__.py` existence:** Tells Python "this directory is a package, not just a random folder." In Python 3.3+, packages can technically work without it (namespace packages), but **always include it** — it's the standard and expected practice.
- **`from .module_a import useful_function`:** The dot (`.`) means "from the current package." This is a **relative import**. It pulls `useful_function` into the package's namespace so users can write `from mypackage import useful_function` instead of `from mypackage.module_a import useful_function`.
- **`__all__`:** A list of names that `from mypackage import *` will export. Without it, `import *` exports everything. With it, only the listed names are exported.

### Relative vs. Absolute Imports

```python
# Inside mypackage/module_b.py

# Relative import (uses dots)
from . import module_a              # Import sibling module
from .module_a import some_function # Import specific name from sibling
from ..other_package import thing   # Go up one level, then into other_package

# Absolute import (uses full path from project root)
from mypackage import module_a
from mypackage.module_a import some_function
```

**Critical rule:** Relative imports (with dots) **only work inside packages**. If you try to use them in a script you run directly (`python module_b.py`), you get:

```
ImportError: attempted relative import with no known parent package
```

This is because a directly-run file has no package context — Python doesn't know what `.` refers to.

---

## 3️⃣ Example 1 — Foundational: Building a Package

### Goal

Create a minimal package from scratch and show how imports work.

### File Structure

```
calculator/
├── __init__.py
├── basic.py
└── advanced.py
```

### `calculator/__init__.py`

```python
# calculator/__init__.py
# This runs when someone does: import calculator

# Expose the most common functions at the package level
from .basic import add, subtract
from .advanced import power

# Now users can do:
#   from calculator import add
# Instead of:
#   from calculator.basic import add
```

### `calculator/basic.py`

```python
# calculator/basic.py

def add(a, b):
    """Return the sum of a and b."""
    return a + b

def subtract(a, b):
    """Return the difference of a and b."""
    return a - b
```

### `calculator/advanced.py`

```python
# calculator/advanced.py

# Relative import: get 'add' from sibling module 'basic'
from .basic import add

def power(base, exponent):
    """Return base raised to exponent using repeated addition (for demo)."""
    result = 1
    for _ in range(exponent):
        result = result * base
    return result

def add_then_power(a, b, exp):
    """Add two numbers, then raise the result to a power."""
    # Uses 'add' imported from basic.py via relative import
    total = add(a, b)
    return power(total, exp)
```

### `main.py` (outside the package)

```python
# main.py — Uses the calculator package

# Thanks to __init__.py, we can import directly from 'calculator'
from calculator import add, subtract, power

print(add(3, 7))           # 10
print(subtract(10, 4))     # 6
print(power(2, 8))         # 256

# We can also import the full module path
from calculator.advanced import add_then_power
print(add_then_power(2, 3, 4))  # (2+3)^4 = 625
```

### Step-by-Step Runtime Walkthrough

1. `from calculator import add, subtract, power` triggers Python to:
   a. Find the `calculator/` directory.
   b. Execute `calculator/__init__.py`.
   c. `__init__.py` runs `from .basic import add, subtract` — loads `basic.py`, pulls `add` and `subtract` into the `calculator` namespace.
   d. `__init__.py` runs `from .advanced import power` — loads `advanced.py`, pulls `power` into the `calculator` namespace.
   e. When `advanced.py` loads, its line `from .basic import add` runs — but `basic.py` is already loaded (cached), so Python reuses it.
   f. Now `add`, `subtract`, and `power` are available in `main.py`.

2. `add(3, 7)` calls the function from `basic.py`. Returns `10`.
3. `subtract(10, 4)` returns `6`.
4. `power(2, 8)` returns `256`.
5. `from calculator.advanced import add_then_power` — `advanced.py` is already loaded, so Python just pulls `add_then_power` into `main.py`'s namespace.
6. `add_then_power(2, 3, 4)` calls `add(2, 3)` → `5`, then `power(5, 4)` → `625`.

### Runtime Visualization

```
main.py namespace:
+----------------------------------+
| add → calculator.basic.add      |
| subtract → calculator.basic.subtract |
| power → calculator.advanced.power    |
| add_then_power → calculator.advanced.add_then_power |
+----------------------------------+

calculator namespace (__init__.py):
+----------------------------------+
| add → basic.add                 |
| subtract → basic.subtract       |
| power → advanced.power          |
+----------------------------------+

calculator.basic namespace:
+----------------------------------+
| add → <function>                |
| subtract → <function>           |
+----------------------------------+

calculator.advanced namespace:
+----------------------------------+
| add → basic.add (relative import)|
| power → <function>              |
| add_then_power → <function>     |
+----------------------------------+
```

### Output

```
10
6
256
625
```

---

## 4️⃣ Example 2 — Real-World: Relative Imports & `__init__.py`

### Goal

Show a realistic package structure, demonstrate the relative import error, and show how `__init__.py` controls the public API.

### Project Structure

```
ecommerce/
├── __init__.py
├── products/
│   ├── __init__.py
│   ├── catalog.py
│   └── pricing.py
├── orders/
│   ├── __init__.py
│   ├── cart.py
│   └── checkout.py
└── utils/
    ├── __init__.py
    └── formatting.py
```

### `ecommerce/utils/formatting.py`

```python
# ecommerce/utils/formatting.py

def format_price(amount):
    """Format a number as a USD price string."""
    return f"${amount:,.2f}"
```

### `ecommerce/products/pricing.py`

```python
# ecommerce/products/pricing.py

# Relative import: go up one level (..), then into utils.formatting
from ..utils.formatting import format_price

def get_discounted_price(original, discount_percent):
    """Calculate and format a discounted price."""
    discounted = original * (1 - discount_percent / 100)
    return format_price(discounted)
```

### `ecommerce/products/__init__.py`

```python
# ecommerce/products/__init__.py

# Expose key functions so users don't need to know internal file structure
from .catalog import list_products
from .pricing import get_discounted_price
```

### `ecommerce/__init__.py`

```python
# ecommerce/__init__.py

# Top-level convenience imports
from .products import get_discounted_price
from .utils.formatting import format_price
```

### Usage from outside the package

```python
# app.py — The entry point

# Clean import thanks to __init__.py chain
from ecommerce import get_discounted_price, format_price

# User doesn't need to know about products/pricing.py or utils/formatting.py
print(get_discounted_price(99.99, 20))   # 20% off $99.99
print(format_price(1234567.89))
```

### Deep Explanation

- **`from ..utils.formatting import format_price`** — The `..` means "go up one package level." From `products/pricing.py`, going up one level reaches `ecommerce/`, then down into `utils/formatting.py`. This only works because `pricing.py` is inside a package.
- **`__init__.py` as a public API:** The `__init__.py` files create a clean facade. Users of the `ecommerce` package write `from ecommerce import get_discounted_price`. They don't need to know that this function lives in `ecommerce/products/pricing.py`. If you later move it to a different file, you only update `__init__.py` — all user code stays the same.
- **Why not run `pricing.py` directly?** If you try `python ecommerce/products/pricing.py`, the relative import `from ..utils.formatting` fails because Python doesn't know `pricing.py` is part of a package. Always run from the project root: `python app.py` or `python -m ecommerce.products.pricing`.

### The Relative Import Error (Common Gotcha)

```bash
$ cd ecommerce/products
$ python pricing.py

Traceback (most recent call last):
  File "pricing.py", line 3, in <module>
    from ..utils.formatting import format_price
ImportError: attempted relative import with no known parent package
```

**Why?** When you run `python pricing.py` directly, Python sets `__name__ = "__main__"` and has no concept of a parent package. The dots in `..utils` have no meaning.

**Fix:** Always run from the project root:

```bash
$ cd /path/to/project
$ python -m ecommerce.products.pricing
```

The `-m` flag tells Python to treat the file as part of a package, preserving the package context.

### Runtime Flow Diagram

```
app.py
  ↓
"from ecommerce import get_discounted_price"
  ↓
Loads ecommerce/__init__.py
  ↓
"from .products import get_discounted_price"
  ↓
Loads ecommerce/products/__init__.py
  ↓
"from .pricing import get_discounted_price"
  ↓
Loads ecommerce/products/pricing.py
  ↓
"from ..utils.formatting import format_price"
  ↓
Loads ecommerce/utils/__init__.py
  ↓
Loads ecommerce/utils/formatting.py
  ↓
All functions now available
  ↓
app.py calls get_discounted_price(99.99, 20)
  ↓
pricing.py calculates: 99.99 * 0.80 = 79.992
  ↓
format_price(79.992) → "$79.99"
  ↓
Output: $79.99
```

### Output

```
$79.99
$1,234,567.89
```

---
---

## Week 2 Summary

| Topic                     | Key Takeaway                                                                 |
|---------------------------|------------------------------------------------------------------------------|
| Recursion vs. Iteration   | Recursion uses the call stack automatically; iteration uses loop variables manually. Choose based on data structure depth. |
| Program Architecture      | `if __name__ == '__main__':` lets files be both runnable scripts and importable libraries. |
| Imports & Namespaces      | Namespaces prevent name collisions. Prefer `import module` over `from module import *`. |
| Packages                  | Packages are directories with `__init__.py`. Relative imports only work inside packages. |
