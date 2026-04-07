# Week 1 Topics: Python Development 
---

## Python Fundamentals & Best Practices
* **Typing & Variables:** * Python is **dynamically but strongly typed**. 
    * Use `snake_case` for variables.
    * Use `UPPERCASE` for constants.
* **Execution:** Run code via terminal (`python script.py`). Use the `if __name__ == "__main__":` block to prevent code from running when imported as a module.
* **Documentation:** * Use **Docstrings** (`"""..."""`) for modules and functions.
    * Implement **type hinting** to improve readability and IDE support.
* **Project Structure:** Organize code into functions, modules, and packages. **Avoid global variables.**

## Data Types & Structures
* **Numeric & String:** * Casting: `int()`, `float()`.
    * Formatting: Use **f-strings** for string interpolation.
    * Methods: `.split()`, `.join()`, `.strip()`.
* **Collections:**
    * **Lists:** Mutable (can be changed).
    * **Tuples:** Immutable (cannot be changed).
    * **Sets:** Unique, unordered elements.
    * **Dictionaries:** Key-value pairs; iterate using `.items()`, `.keys()`, or `.values()`.
* **Mutability Pitfalls:** Never modify a list while iterating over it, and avoid using mutable objects (like lists) as default function arguments.


## Control Flow & Functions
* **Iteration:** Iterate directly over collections (`for item in list:`) rather than using `range(len(list))`.
* **Functions:** Understand the difference between **positional** and **keyword** arguments.
* **Error Handling:** Use `try...except` blocks. **Avoid bare `except:` clauses**; always specify the exception type.


## File Operations
* **Context Managers:** Use the `with` statement (`with open(...) as f:`) to handle file closing automatically.
* **Reading Methods:**
    * `readlines()`: Loads the entire file into a list of strings.
    * **File Iteration:** Loop directly over the file object for better memory efficiency with large files.
* **Path Handling:** Use `os.path` (or `pathlib`) for relative paths to ensure the code works on different operating systems.


## Command Line Interfaces (CLI)
* **Argparse Module:** The standard library tool for parsing command-line arguments.
* **Argument Types:**
    * **Positional:** Required, identified by their order (no flags).
    * **Optional/Flags:** Identified by short (`-h`) or long (`--help`) flags.
* **Advanced Configuration:**
    * **Mutually Exclusive Groups:** Use these when two flags cannot be used at the same time.
    * **Actions:** Use `action="store_true"` for simple boolean switches/flags.
## [Week 1 Notes](./Notes/W1_Notes.md)


# Week 2 Topics: Program Architecture & Recursion
---

## Recursion vs. Iteration
* **Core Concepts:** * **Base Case:** The condition that stops the recursion.
    * **Recursive Case:** The part of the function that calls itself to continue the process.
* **Stack Management:** * **Recursion:** Uses the system's **call stack** automatically.
    * **Iteration:** For complex data (like deeply nested lists), iteration often requires manually managing an **explicit stack**.
* **Use Case:** Recursion is often more intuitive for data structures with arbitrary depth (nested lists, trees), while iteration offers more manual control and can be more memory-efficient.


## Python Program Architecture
* **Scripts vs. Libraries:** * **Scripts:** Intended for direct execution.
    * **Libraries:** Modules intended to be imported by other programs.
* **Execution Control:** The `if __name__ == "__main__":` block allows a file to serve as both a script (running code) and a library (providing functions without side effects on import).
* **Bytecode:** Python compiles imported modules into **bytecode** (`.pyc` files) inside a `__pycache__` directory to optimize loading times.


## Imports & Namespaces
* **Namespaces:** Modules provide isolated scopes to prevent variable naming conflicts (e.g., `math.sqrt` vs. a custom `sqrt`).
* **Search Path:** Python looks for modules in the current directory, `PYTHONPATH`, and standard library paths—all accessible via `sys.path`.
* **Import Methods:**
    * **Aliasing:** `import numpy as np`
    * **Specific Members:** `from math import sqrt`
    * **Wildcards:** `from module import *` (Generally **discouraged** as it can clutter the namespace and cause conflicts).

## Packages
* **Structure:** A package is a directory containing Python modules, typically including an `__init__.py` file.
* **`__init__.py`:** Executes upon package import; used to expose specific sub-package members to the user.
* **Relative Imports:** Uses dot notation (e.g., `from .module import func`). 
    * *Note:* These only work within packages and will fail if the script is executed directly.
 
## [Week 2 Notes](./Notes/W2_Notes.md)



# Week 3 Topics: Advanced Functions & Modern Workflow

---

## Advanced Function Arguments
* **Variable Positional Arguments (`*args`):** Collects extra positional arguments into a **tuple**.
* **Variable Keyword Arguments (`**kwargs`):** Collects extra keyword arguments into a **dictionary**.
* **Unpacking Operators:** * Use `*` to unpack iterables (lists/tuples) into arguments.
    * Use `**` to unpack dictionaries into keyword arguments.
* **Parameter Ordering:** To avoid syntax errors, follow this strict order:
    1. Regular/Positional parameters
    2. `*args`
    3. `**kwargs`


## Higher Order Functions & Decorators
* **Higher Order Functions:** Functions that either accept other functions as arguments or return a function as a result.
* **Closures:** Inner functions that "remember" and can access variables from their enclosing scope even after the outer function has completed execution.
* **Decorators:** Syntactic sugar (`@decorator_name`) used to wrap a function. This modifies behavior (like logging or timing) without changing the source code.
    
* **Metadata Preservation:** Always use `@functools.wraps` inside a decorator to ensure the wrapped function keeps its original `__name__` and `__doc__` attributes.
* **Use Cases:** Logging, input validation, execution timing, and **memoization** (caching).



## Modern Project Management & Environment
* **Virtual Environments (`.venv`):** Essential for isolating project-specific dependencies from your global system Python.
* **Dependency Management (`uv`):** A high-performance alternative to `pip`.
    * **`pyproject.toml`:** Central configuration file.
    * **`uv.lock`:** Ensures deterministic builds by locking specific dependency versions.
* **Secrets Management:** * Store sensitive data (API keys, DB credentials) in a `.env` file.
    * Use `python-dotenv` to load these into environment variables.
* **Security:** **Never** commit secrets. Always add `.env` to your `.gitignore` file.
    
* **Debugging:** Use VS Code's `launch.json` to configure debuggers, set breakpoints, and monitor variable states in real-time.

## [Week 3 Notes](./Notes/W3_Notes.md)






# Week 4 Topics: Automated Testing with Pytest

---

## Automated Testing Fundamentals
* **Core Concepts:** Automated testing provides confidence and speed by catching bugs early compared to manual testing.
* **Unit Tests:** These are fast, isolated, and repeatable tests designed to verify individual functions or classes in isolation.
* **Test-Driven Development (TDD):** A cycle consisting of three stages:
    1.  **Red:** Write a failing test.
    2.  **Green:** Write just enough code to make the test pass.
    3.  **Refactor:** Clean up and improve the code while keeping it functional.

* **Assertions:** Using `assert` to verify conditions. Unlike standard Python asserts, **pytest** provides detailed introspection (showing exactly where the actual value differs from the expected value).



## Pytest Framework
* **Installation:** Install as a development dependency: `uv add --dev pytest`.
* **Execution:** Run via `uv run pytest` or simply `pytest` if your `.venv` is active.
* **Discovery Rules:** Pytest automatically finds tests if you follow these naming conventions:
    * **Files:** `test_*.py` or `*_test.py`.
    * **Functions:** Must start with `test_`.
    * **Classes:** Must start with `Test`.
* **Handling Exceptions:** Use the `pytest.raises` context manager to verify that your code correctly throws errors:
    ```python
    with pytest.raises(ValueError):
        my_function(-1)
    ```



## Fixtures & Setup
* **Purpose:** Fixtures handle reusable setup (e.g., creating a database connection) and teardown code to keep tests DRY (Don't Repeat Yourself).
* **Implementation:** Defined with `@pytest.fixture` and passed as arguments to test functions.
* **Scopes:**
    * `function` (default): Runs once for every single test.
    * `module`: Runs once per test file.
    * `session`: Runs once for the entire test run.
* **Built-in Fixtures:**
    * `tmp_path`: Provides a temporary directory unique to each test (great for file I/O tests).
    * `capsys`: Captures `stdout` and `stderr` so you can verify what a function prints.
* **Sharing Fixtures:** Use a `conftest.py` file to make fixtures available across multiple files without needing to import them.
## [Week 4 Notes](./Notes/W4_Notes.md)





# Week 6 Topics: Data Serialization & Exception Handling

---

## Working with JSON
* **Syntax & Pitfalls:** JSON is a language-independent data format. Common pitfalls include trailing commas (not allowed) and the requirement for double quotes (`"`) around keys and strings.
* **Writing JSON (Serialization):**
    * Use `json.dumps()` to convert a Python dictionary to a JSON string.
    * Use `json.dump()` to write data directly to a `.json` file.
    * **Serialization:** Note that some Python types (like `datetime` or custom classes) require a custom encoder to be converted to JSON.
* **Reading JSON (Deserialization):**
    * Use `json.loads()` to parse a JSON string into a Python dictionary.
    * Use `json.load()` to read an external JSON file.
* **Interacting & Tools:**
    * **Prettify:** Use `indent=4` in your dump methods for readability.
    * **CLI Tools:** Use `python -m json.tool` in the terminal to validate and pretty-print JSON files.
    * **Minify:** Remove whitespace/indentation to reduce file size for transmission.



## Working with CSV
* **Standard Library (`csv`):**
    * **Reading:** Use `csv.reader` for list-based access or `csv.DictReader` to map rows to dictionaries.
    * **Writing:** Use `csv.writer` or `csv.DictWriter`. 
    * **Parameters:** Adjust for different delimiters (e.g., tabs vs. commas) or quoting styles.
* **Pandas Library:** A powerful tool for data analysis that simplifies CSV tasks.
    * `pd.read_csv()`: Loads a file into a DataFrame.
    * `df.to_csv()`: Exports a DataFrame back to a file.
    * **Note:** Pandas is preferred for large datasets or complex data manipulation.



## Advanced Exception Handling
* **Exception Context:** Python tracks the "cause" of exceptions when one error triggers another (using `from` for explicit chaining).
* **Built-in Exceptions & Hierarchy:**
    * **Base Classes:** All exceptions inherit from `BaseException`. Most user-defined and common errors (like `ValueError`) inherit from `Exception`.
    * **Concrete Exceptions:** Specific errors like `KeyError`, `IndexError`, and `TypeError`.
    * **OS Exceptions:** Related to system errors like `FileNotFoundError` or `PermissionError`.
* **Inheritance:** You can create custom exceptions by inheriting from the `Exception` class:
    ```python
    class MyCustomError(Exception):
        pass
    ```
* **Exception Groups:** Introduced in Python 3.11, allowing multiple unrelated exceptions to be raised and handled simultaneously using `except*`.
* **Warnings:** Used to alert users of deprecated features or potential issues without stopping execution.
## [Week 6 Notes](./Notes/W6_Notes.md)

## Object Oriented Programming
# OOP & UML — Bullet Point Summaries

---



## (Week 9) Object-Oriented Programming — Core Concepts

* **What is OOP:** A programming paradigm that organizes code around **objects** — bundles of data (attributes) and behavior (methods) — replacing procedural code where data and functions are scattered and unprotected.
* **The Five Pillars:**
    * **Modelization:** Describe real-world things using only the attributes relevant to your problem.
    * **Encapsulation:** Bundle data + methods together; control access to internal state.
    * **Abstraction:** Expose a simple public interface; hide the complex machinery underneath.
    * **Inheritance:** A child class reuses and extends a parent class ("is-a" relationship).
    * **Polymorphism:** The same method name behaves differently depending on which object calls it.

## Classes & Objects

* **Class vs Object:** A **class** is a blueprint (defined once); an **object** is a specific instance created from it — each with its own independent copy of the data.
* **`__init__`:** The initializer method — runs automatically when an object is created to set up its starting state. It is *not* the constructor; Python creates the object first, then calls `__init__`.
* **`self`:** A reference to the current instance. Always the first parameter of instance methods — Python passes it automatically; you never pass it yourself.
* **Class vs Instance Attributes:**
    * **Instance attributes** (`self.name`): Unique to each object — defined inside `__init__`.
    * **Class attributes** (`ClassName.count`): Shared across all instances — defined at the class level. Use `ClassName.attr += 1`, *not* `self.attr += 1`, to update them.

## Method Types

* **Instance Methods:** Default type; receives `self`; can access and modify both instance and class data.
* **Class Methods:** Decorated with `@classmethod`; receives `cls` (the class itself); used for **alternative constructors** or managing class-wide state.
* **Static Methods:** Decorated with `@staticmethod`; receives nothing; pure utility functions logically grouped inside the class but needing no instance or class data.

| Method Type | Decorator | First Param | Access |
|---|---|---|---|
| Instance | *(none)* | `self` | Instance + class data |
| Class | `@classmethod` | `cls` | Class data only |
| Static | `@staticmethod` | *(none)* | Neither |

## Dunder (Magic) Methods

* **What they are:** Methods named with double underscores (`__str__`, `__repr__`, `__add__`) that Python calls **automatically** in response to built-in operations — never call them directly.
* **`__str__`:** Defines user-friendly display; called by `print()` and f-strings.
* **`__repr__`:** Defines developer/debug display; called by `repr()`, the interactive shell, and when objects appear inside containers. **Always define this at minimum.**
* **Fallback rule:** If `__str__` is missing, Python falls back to `__repr__`.
* **Operator overloading:** Define `__add__`, `__eq__`, `__lt__`, etc. to make custom objects work with `+`, `==`, `<`, and other operators.

## Encapsulation

* **Purpose:** Protect internal state — all data changes must go through controlled methods, preventing accidental corruption.
* **Python's privacy model** (convention-based, not enforced):
    * `self.name` — **Public:** accessible anywhere.
    * `self._name` — **Protected:** signals "internal use only"; subclasses may access it.
    * `self.__name` — **Private:** Python name-mangles to `self._ClassName__name`; use sparingly.
* **Prefer single underscore (`_`)** in most cases; double underscore (`__`) is reserved for preventing naming conflicts in complex inheritance hierarchies.

## `@property` Decorator

* **What it does:** Turns a method into a **virtual attribute** — looks like plain data access from outside, but secretly runs a method.
* **Why use it:** Gives you clean attribute syntax (`obj.balance`) *and* the safety of a method, without forcing callers to use `obj.get_balance()`.
* **Pattern:**
    * `@property` defines the **getter** (read access).
    * `@x.setter` defines the **setter** (write access with validation).
    * Omitting the setter makes the property **read-only** — attempts to set it raise `AttributeError`.

## Abstraction

* **Goal:** Users interact with a simple interface (`tv.channel = 5`) without knowing the validation logic, state checks, or internal variables running underneath.
* **How:** Combine `@property`, `@x.setter`, and private/protected attributes so complexity is hidden behind clean public methods.
* **Abstraction vs Encapsulation:**
    * Encapsulation asks *"who can touch this?"*
    * Abstraction asks *"what does this do (not how)?"*

## Object Relationships

* **Composition ("part-of", strong):** The contained object is **created inside** the container and **destroyed with it**. Example: `Order` owns `LineItem` — no order means no line items.
* **Aggregation ("has-a", weak):** The contained object is **created externally**, **passed in**, and **survives** if the container is deleted. Example: `Department` references `Professor` — closing a department doesn't delete the professors.

| | Composition | Aggregation |
|---|---|---|
| Relationship | Strong ownership | Weak reference |
| Object created | Inside container | Outside container |
| Lifecycle | Dies with container | Independent |
| UML symbol | Filled diamond | Hollow diamond |

## Inheritance

* **"Is-a" relationship:** A child class IS A more specific version of the parent — it inherits all parent attributes and methods for free.
* **`super().__init__()`:** Always call this first inside the child's `__init__` to initialize parent attributes — skipping it leaves the parent data uncreated.
* **Method overriding:** A child can replace a parent method by defining a method with the same name — Python always calls the most specific version.
* **Inheritance chain lookup:** If a method isn't found on the child, Python walks up the chain to the parent, then grandparent, and so on.

## Polymorphism

* **One interface, many behaviors:** The same method call (`shape.draw()`) produces different results depending on the actual object type (`Circle`, `Square`, `Triangle`).
* **Key benefit:** Write code that works on *any* object sharing an interface — adding a new subtype never requires changing existing code.
* **Avoid:** `if type(obj) == Dog: ... elif type(obj) == Cat: ...` — this is the anti-pattern that polymorphism replaces.

## Abstract Base Classes (ABCs)

* **What:** A class that **cannot be instantiated** directly and **forces subclasses** to implement specific methods.
* **How:** Inherit from `ABC` (from the `abc` module) and decorate required methods with `@abstractmethod`.
* **Why:** Enforces a contract — if a subclass forgets to implement an abstract method, Python raises `TypeError` at class-definition time, not silently at runtime.
* **Common use cases:** Defining swappable backends (storage, payment processors, loggers) where all implementations must share the same interface.

---

## UML Class Diagrams

* **What:** Standardized visual blueprints showing classes, attributes, methods, and relationships — the architectural drawings of software design.
* **Why use it:** Design before coding, communicate structure to teammates, document systems visually, and spot design problems early.

## Anatomy of a Class Box

* Each class is a **rectangle with three sections**: class name (top), attributes (middle), methods (bottom).
* **Visibility symbols:**
    * `+` Public
    * `-` Private
    * `#` Protected
    * `~` Package (not directly applicable in Python)
* Always include **types** for attributes and **return types** for methods.
* In most course settings, **only the public interface** (`+` members) is shown — private internals are hidden.

## Python-Specific UML Conventions

* **`@property`:** Placed in the **methods section** with a `~property~` stereotype tag — it looks like an attribute but runs a method.
* **Static methods:** Marked with `$` suffix.
* **Abstract methods:** Marked with `*` suffix.
* **Class attributes:** Labeled with `~class attr~` stereotype.

## UML Relationships

* **Association (`--`):** Basic "uses" relationship — one class references another; no ownership on either side.
* **Aggregation (`--o`):** "Has-a" weak relationship — part exists independently; **hollow diamond** on the container side.
* **Composition (`--*`):** "Part-of" strong relationship — part is created inside and dies with the whole; **filled diamond** on the container side.
* **Inheritance (`<|--`):** "Is-a" relationship — **hollow arrowhead pointing to the parent class**.

## Multiplicity

* Labels how many instances of each class participate in a relationship.

| Notation | Meaning |
|---|---|
| `1` | Exactly one |
| `0..1` | Zero or one (optional) |
| `0..*` or `*` | Zero or more |
| `1..*` | One or more |
| `2..5` | Between 2 and 5 |

## Abstract Classes & Interfaces in UML

* **Abstract classes:** Use `<<abstract>>` stereotype on the class; mark abstract methods with `*`.
* **Interfaces:** Use `<<interface>>` stereotype; shown with a dashed inheritance arrow (`<|..`).

## Tools

* **Mermaid:** Text-based diagrams rendered in the browser or VS Code — used in this course.
* **PlantUML:** More feature-rich text-based alternative.
* **pyreverse:** Part of `pylint`; auto-generates UML diagrams directly from existing Python code — use to keep diagrams in sync.

## Best Practices & Pitfalls

* **Do:** Start simple and add detail incrementally; show only important relationships; update diagrams as code evolves using `pyreverse`.
* **Avoid:** Over-engineering diagrams for small scripts; showing every attribute and method (too much noise); treating diagrams as static — they should evolve with the code.

## [Week 9 Notes](./Notes/W9_Notes.md)

---

# Inheritance & ABCs — Summary Bullet Points

## 1. Inheritance
- A **subclass** (child) automatically receives all attributes and methods from a **superclass** (parent)
- Syntax: `class Child(Parent):`
- Solves **code duplication** — shared logic lives in one parent class (DRY principle)
- Models **IS-A** relationships: "A Dog is an Animal" → `class Dog(Animal)`
- All Python classes implicitly inherit from `object`
- Inheritance is **transitive**: if `Dog → Animal → object`, then `Dog` is also an `object`
- If "X is a Y" doesn't sound natural, use **composition** instead of inheritance

## 2. `super().__init__()` — Parent Constructor
- `super()` returns a proxy to the parent class, letting you call its methods
- `super().__init__(...)` runs the parent's constructor to set up foundational attributes
- **Without it**, parent attributes (e.g., `self._name`) are never created → `AttributeError`
- **Rule of thumb**: always call `super().__init__(...)` as the **first line** of the child's `__init__`
- Only pass arguments the parent expects — not child-specific ones

## 3. Method Overriding
- A subclass **overrides** a parent method by defining a method with the **same name**
- Python uses the **most specific version first** (child before parent)
- Two common parent patterns:
  - **Placeholder** that raises `NotImplementedError` (child must replace it)
  - **Generic default** that a child specializes
- Beware of **typos** — `def speek()` creates a new method instead of overriding `speak()`

## 4. Extending a Parent Method with `super()`
- **Extension** = run the parent's method + add extra steps (vs. full override = replace entirely)
- Pattern: `result = super().method()` → then add child-specific logic
- Follows DRY — reuses parent logic, child only adds what's unique
- `super()` can be called at any point in the method, not just the first line (unlike `__init__`)

## 5. Polymorphism
- **Polymorphism** = same method call, different behaviour depending on the object's class
- Lets one loop/function work with **any subclass** uniformly — no type-checking needed
- **Future-proof**: adding a new subclass requires zero changes to existing code
- Python uses **duck typing** — any object with the right method works, inheritance not strictly required

## 6. `isinstance()`
- `isinstance(obj, ClassName)` → `True` if `obj` is that class **or any subclass** of it
- `isinstance(obj, (ClassA, ClassB))` → `True` if `obj` is an instance of **either**
- Use it to safely access **subclass-specific** attributes or guard **dunder methods** (`__eq__`)
- `isinstance()` respects inheritance; `type(obj) == Class` does **not** (almost always prefer `isinstance`)
- `NotImplemented` (return value) ≠ `NotImplementedError` (exception) — common beginner mix-up

## 7. Abstract Base Classes (ABCs)
- **ABC** = a strict blueprint that **cannot be instantiated** directly
- Import: `from abc import ABC, abstractmethod`
- `@abstractmethod` forces subclasses to implement a method — error at **object creation**, not runtime
- **Concrete methods** in an ABC are inherited normally (e.g., `log()`)
- **Abstract properties**: stack `@property` on top, `@abstractmethod` below (order matters)
- Abstract methods **can have a body** — subclasses access it via `super().method()`
- ABCs catch missing methods **immediately** (`TypeError`) vs. `NotImplementedError` which only crashes when called

## 8. Multiple Inheritance & Mixins
- A class can inherit from **multiple parents**: `class Child(ParentA, ParentB)`
- **Mixin** = small, focused class that adds one specific capability (logging, serialization, etc.)
- Naming convention: `SomethingMixin`
- **MRO (Method Resolution Order)**: Python searches child → left parent → right parent → ... → `object`
- **List mixins before the main parent**: `class MyClass(MixinA, MainParent)`
- Inspect MRO with `ClassName.__mro__`
- Python uses **C3 linearization** to handle diamond inheritance correctly
- Keep mixins small, stateless (no `__init__`), and focused on one responsibility
## [Week 10 Notes](./Notes/W10_Notes.md)

---
