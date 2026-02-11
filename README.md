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

