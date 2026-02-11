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

---

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

---

## Control Flow & Functions
* **Iteration:** Iterate directly over collections (`for item in list:`) rather than using `range(len(list))`.
* **Functions:** Understand the difference between **positional** and **keyword** arguments.
* **Error Handling:** Use `try...except` blocks. **Avoid bare `except:` clauses**; always specify the exception type.

---

## File Operations
* **Context Managers:** Use the `with` statement (`with open(...) as f:`) to handle file closing automatically.
* **Reading Methods:**
    * `readlines()`: Loads the entire file into a list of strings.
    * **File Iteration:** Loop directly over the file object for better memory efficiency with large files.
* **Path Handling:** Use `os.path` (or `pathlib`) for relative paths to ensure the code works on different operating systems.

---

## Command Line Interfaces (CLI)
* **Argparse Module:** The standard library tool for parsing command-line arguments.
* **Argument Types:**
    * **Positional:** Required, identified by their order (no flags).
    * **Optional/Flags:** Identified by short (`-h`) or long (`--help`) flags.
* **Advanced Configuration:**
    * **Mutually Exclusive Groups:** Use these when two flags cannot be used at the same time.
    * **Actions:** Use `action="store_true"` for simple boolean switches/flags.
## [Notes](./Notes/W1_Notes.md)
