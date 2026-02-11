# Week 6 — JSON, CSV, and Python Exceptions

---

## Table of Contents

1. [Introducing JSON](#1-introducing-json)
2. [JSON Syntax & Pitfalls](#2-json-syntax--pitfalls)
3. [Writing JSON With Python](#3-writing-json-with-python)
4. [Reading JSON With Python](#4-reading-json-with-python)
5. [Interacting With JSON](#5-interacting-with-json)
6. [What Is a CSV File?](#6-what-is-a-csv-file)
7. [Parsing CSV Files With Python's Built-in CSV Library](#7-parsing-csv-files-with-pythons-built-in-csv-library)
8. [Writing CSV Files With csv](#8-writing-csv-files-with-csv)
9. [Parsing & Writing CSV Files With pandas](#9-parsing--writing-csv-files-with-pandas)
10. [Built-in Exceptions](#10-built-in-exceptions)
11. [Exception Context](#11-exception-context)
12. [Inheriting From Built-in Exceptions](#12-inheriting-from-built-in-exceptions)
13. [Base Classes & Concrete Exceptions](#13-base-classes--concrete-exceptions)
14. [OS Exceptions & Warnings](#14-os-exceptions--warnings)
15. [Exception Groups](#15-exception-groups)
16. [Exception Hierarchy](#16-exception-hierarchy)

---

## 1. Introducing JSON

### Concept Overview

**Definition**
**JSON** (JavaScript Object Notation) is a lightweight, text-based data format used to store and exchange structured data between systems. Despite its name, JSON is **language-independent** — Python, Java, Go, Rust, and virtually every programming language can read and write JSON.

**Why It Exists**
Programs need to send data to each other. A Python dictionary lives in memory — it can't travel over the internet or be saved to a file as-is. JSON solves this by providing a **universal text format** that any language can produce and consume.

```
Problem: How do two programs share data?

Program A (Python)          Program B (JavaScript)
┌──────────────────┐        ┌──────────────────┐
│ {"name": "Alice"}│   ???  │ {name: "Alice"}  │
│ (Python dict)    │ ──────→│ (JS object)      │
└──────────────────┘        └──────────────────┘

Solution: JSON — a common language both understand

Program A (Python)          JSON (text)              Program B (JavaScript)
┌──────────────┐    serialize    ┌──────────────┐    parse    ┌──────────────┐
│ Python dict  │ ──────────────→ │ {"name":     │ ─────────→ │ JS object    │
│              │                 │  "Alice"}    │            │              │
└──────────────┘                 └──────────────┘            └──────────────┘
```

**Mental Model**
Think of JSON as a **universal translator** at the United Nations. Every country (programming language) speaks differently, but the translator (JSON) converts everyone's words into a common written form that all can read.

**When To Use It**
- Sending data between a web server and a browser (APIs).
- Configuration files (VS Code's `settings.json`, `package.json`).
- Storing structured data that humans might need to read.
- Any time you need a portable, text-based data format.

**When NOT To Use It**
- Storing large datasets (use CSV, Parquet, or databases instead).
- Binary data like images or audio (JSON is text-only).
- When performance is critical (JSON parsing is slower than binary formats like Protocol Buffers).
- When you need comments in your config file (JSON doesn't support comments — use TOML or YAML).

**Comparison With Similar Formats**

| Format | Human Readable | Supports Comments | Use Case |
|---|---|---|---|
| **JSON** | Yes | No | APIs, config, data exchange |
| **CSV** | Yes | No | Tabular data (rows & columns) |
| **YAML** | Yes | Yes | Config files (Docker, CI/CD) |
| **TOML** | Yes | Yes | Python project config (`pyproject.toml`) |
| **XML** | Somewhat | Yes | Legacy systems, SOAP APIs |
| **Pickle** | No (binary) | No | Python-only object serialization |

---

## 2. JSON Syntax & Pitfalls

### Concept Overview

**Definition**
JSON syntax defines the rules for how data must be formatted in a JSON document. It looks similar to Python dictionaries, but there are **critical differences** that trip up beginners.

### 2️⃣ Syntax & Structure

**The Six JSON Data Types:**

```
JSON Type        Example                  Python Equivalent
─────────────────────────────────────────────────────────────
object           {"key": "value"}         dict
array            [1, 2, 3]               list
string           "hello"                  str
number           42, 3.14                 int / float
boolean          true, false              True, False
null             null                     None
```

**Canonical JSON Syntax:**

```json
{
    "name": "Alice",
    "age": 30,
    "is_student": false,
    "courses": ["math", "science"],
    "address": {
        "city": "Portland",
        "zip": "97201"
    },
    "nickname": null
}
```

**Line-by-Line Breakdown:**

- `{` — Every JSON document starts with `{` (object) or `[` (array). Most API responses start with `{`.
- `"name": "Alice"` — Keys **must** be strings in double quotes. Values can be any of the six types.
- `"age": 30` — Numbers have no quotes. `30` is a number; `"30"` would be a string.
- `"is_student": false` — Booleans are lowercase: `true`/`false`, NOT `True`/`False`.
- `"courses": ["math", "science"]` — Arrays (lists) use square brackets.
- `"address": {...}` — Objects can be nested inside other objects.
- `"nickname": null` — `null` represents "no value." Lowercase, NOT `None`.

---

### JSON Syntax Pitfalls

**These are the mistakes that will break your JSON and waste hours of debugging:**

**Pitfall 1: Single Quotes**

```
WRONG:  {'name': 'Alice'}     ← Python-style single quotes
RIGHT:  {"name": "Alice"}     ← JSON requires DOUBLE quotes only
```

**Pitfall 2: Trailing Commas**

```
WRONG:  {"name": "Alice", "age": 30,}    ← trailing comma after 30
RIGHT:  {"name": "Alice", "age": 30}     ← no trailing comma
```

Python allows trailing commas in dicts and lists. JSON does **not**.

**Pitfall 3: Python Booleans and None**

```
WRONG:  {"active": True, "deleted": None}     ← Python syntax
RIGHT:  {"active": true, "deleted": null}      ← JSON syntax
```

**Pitfall 4: Unquoted Keys**

```
WRONG:  {name: "Alice"}       ← key without quotes (JavaScript allows this)
RIGHT:  {"name": "Alice"}     ← JSON requires quoted keys
```

**Pitfall 5: Comments**

```
WRONG:  {
            "name": "Alice"   // this is a user
        }
RIGHT:  {
            "name": "Alice"
        }
```

JSON has **no comment syntax**. Not `//`, not `#`, not `/* */`. Nothing.

**Summary Table:**

| Pitfall | Python | JSON |
|---|---|---|
| Strings | `'single'` or `"double"` | `"double"` only |
| True/False | `True` / `False` | `true` / `false` |
| None/null | `None` | `null` |
| Trailing comma | Allowed | **Forbidden** |
| Comments | `#` | **Not supported** |
| Keys | Any hashable type | **Strings only** |

---

## 3. Writing JSON With Python

### Concept Overview

**Definition**
**Serialization** is the process of converting a Python object (dict, list, etc.) into a JSON-formatted string. Python's built-in `json` module handles this.

**Why It Exists**
Python objects live in memory. To send them over a network or save them to a file, you must convert them to text. The `json` module's `dumps()` and `dump()` functions do this conversion.

**Mental Model**

```
Python World                    JSON World (text)
┌──────────────┐   serialize    ┌──────────────┐
│ dict, list,  │ ─────────────→ │ JSON string  │
│ int, str,    │   json.dumps() │              │
│ bool, None   │                │              │
└──────────────┘                └──────────────┘
```

### 2️⃣ Syntax & Structure

**Two Functions for Writing JSON:**

```python
import json

# dumps() → returns a JSON STRING (the 's' stands for 'string')
json_string = json.dumps(python_object)

# dump() → writes JSON directly to a FILE
json.dump(python_object, file_object)
```

**Type Conversion Table (Python → JSON):**

```
Python Type          JSON Type
──────────────────────────────
dict          →      object
list, tuple   →      array
str           →      string
int, float    →      number
True          →      true
False         →      false
None          →      null
```

**Important:** `tuple` becomes a JSON array. When you read it back, it comes back as a `list`, not a `tuple`. This is a **lossy conversion**.

### 3️⃣ Example 1 — Foundational

**Goal:** Convert a Python dictionary to a JSON string.

```python
import json

# A Python dictionary
user = {
    "name": "Alice",
    "age": 30,
    "is_active": True,
    "scores": [95, 87, 92],
    "address": None
}

# Serialize to JSON string
json_string = json.dumps(user)
print(json_string)
print(type(json_string))
```

**Step-by-Step Runtime Walkthrough:**

1. `import json` — Python loads the built-in `json` module into memory.
2. `user = {...}` — A Python dictionary is created in memory. Note: `True` (capital T), `None` (capital N) — these are Python syntax.
3. `json.dumps(user)` — The `dumps` function walks through the dictionary:
   - `"name"` (str) → stays `"name"` (JSON string).
   - `"Alice"` (str) → stays `"Alice"`.
   - `30` (int) → stays `30` (JSON number).
   - `True` (Python bool) → converted to `true` (JSON boolean).
   - `[95, 87, 92]` (list) → stays `[95, 87, 92]` (JSON array).
   - `None` (Python NoneType) → converted to `null` (JSON null).
4. The result is a **string** — not a dict, not a file, just text.
5. `type(json_string)` confirms it's `<class 'str'>`.

**Runtime Visualization:**

```
json.dumps(user)
│
├─ Walk through dict:
│   ├─ "name": "Alice"    → "name": "Alice"     (no change)
│   ├─ "age": 30          → "age": 30           (no change)
│   ├─ "is_active": True  → "is_active": true   (True → true)
│   ├─ "scores": [95,87,92] → "scores": [95,87,92] (no change)
│   └─ "address": None    → "address": null     (None → null)
│
└─ Return: '{"name": "Alice", "age": 30, "is_active": true, "scores": [95, 87, 92], "address": null}'
```

**Output:**

```
{"name": "Alice", "age": 30, "is_active": true, "scores": [95, 87, 92], "address": null}
<class 'str'>
```

Notice: `True` became `true`, `None` became `null`. The `json` module handles the conversion automatically.

---

### Serialize Other Python Data Types

```python
import json

# Tuples become arrays
data = {"coordinates": (10, 20)}
print(json.dumps(data))
# Output: {"coordinates": [10, 20]}    ← tuple became array!

# Nested structures work
complex_data = {
    "users": [
        {"name": "Alice", "active": True},
        {"name": "Bob", "active": False}
    ],
    "count": 2
}
print(json.dumps(complex_data))
```

**What CANNOT Be Serialized:**

```python
import json
from datetime import datetime

# These will CRASH with TypeError:
json.dumps({"date": datetime.now()})      # datetime not serializable
json.dumps({"data": {1, 2, 3}})           # set not serializable
json.dumps({"data": b"hello"})            # bytes not serializable
```

**Why?** JSON only has six types. Python's `datetime`, `set`, and `bytes` have no JSON equivalent. You must convert them manually:

```python
import json
from datetime import datetime

data = {
    "date": datetime.now().isoformat(),   # Convert to string first
    "tags": list({1, 2, 3}),              # Convert set to list first
}
print(json.dumps(data))
# Output: {"date": "2025-01-15T10:30:00", "tags": [1, 2, 3]}
```

---

### Write a JSON File With Python

### 2️⃣ Syntax & Structure

```python
import json

data = {"name": "Alice", "age": 30}

with open("user.json", "w") as file:
    json.dump(data, file)
```

**Line-by-Line Breakdown:**

- `open("user.json", "w")` — Opens (or creates) a file named `user.json` in **write** mode.
- `as file` — The file object is assigned to the variable `file`.
- `json.dump(data, file)` — Note: `dump` (no 's'). Writes the JSON directly to the file. This is more memory-efficient than `json.dumps()` + `file.write()` for large data.

**`dump` vs. `dumps` — The Critical Difference:**

```
json.dumps(data)         → returns a STRING     (s = string)
json.dump(data, file)    → writes to a FILE     (no s = file)
```

### 4️⃣ Example 2 — Real-World / Interview Level

**Goal:** Build a function that saves application configuration to a JSON file with proper formatting and error handling.

```python
import json
from pathlib import Path

def save_config(config, filepath="config.json"):
    """
    Save configuration dictionary to a JSON file.

    Args:
        config: Dictionary containing configuration data.
        filepath: Path to the output file (default: config.json).

    Raises:
        TypeError: If config contains non-serializable types.
        OSError: If the file cannot be written.
    """
    path = Path(filepath)

    # Ensure parent directory exists
    path.parent.mkdir(parents=True, exist_ok=True)

    with open(path, "w", encoding="utf-8") as file:
        json.dump(
            config,
            file,
            indent=4,           # Pretty-print with 4-space indentation
            sort_keys=True,     # Alphabetize keys for consistency
            ensure_ascii=False  # Allow non-ASCII characters (é, ñ, 日本語)
        )

    print(f"Config saved to {path}")


# Usage
app_config = {
    "database": {
        "host": "localhost",
        "port": 5432,
        "name": "myapp_db"
    },
    "debug": False,
    "allowed_origins": ["http://localhost:3000", "https://myapp.com"],
    "version": "2.1.0"
}

save_config(app_config)
```

**Deep Explanation:**

- `indent=4` — Without this, JSON is written as one long line. `indent=4` adds newlines and 4-space indentation, making the file human-readable.
- `sort_keys=True` — Alphabetizes dictionary keys. This makes diffs in version control cleaner — if two developers save the same config, the output is identical.
- `ensure_ascii=False` — By default, `json.dump` escapes non-ASCII characters (e.g., `é` becomes `\u00e9`). Setting this to `False` keeps the original characters.
- `encoding="utf-8"` — Ensures the file can store any Unicode character.
- `path.parent.mkdir(parents=True, exist_ok=True)` — Creates any missing parent directories. Without this, saving to `"settings/config.json"` would fail if the `settings/` folder doesn't exist.

**Runtime Flow Diagram:**

```
save_config(app_config, "config.json")
│
├─ path = Path("config.json")
├─ path.parent = Path(".")  (current directory)
├─ mkdir(parents=True, exist_ok=True) → no-op (directory exists)
│
├─ open("config.json", "w", encoding="utf-8")
│   └─ Creates/overwrites config.json
│
├─ json.dump(config, file, indent=4, sort_keys=True, ...)
│   ├─ Sort keys alphabetically
│   ├─ Convert Python → JSON types
│   ├─ Format with 4-space indentation
│   └─ Write to file
│
├─ File closed (with block exits)
└─ print("Config saved to config.json")
```

**Output (config.json file contents):**

```json
{
    "allowed_origins": [
        "http://localhost:3000",
        "https://myapp.com"
    ],
    "database": {
        "host": "localhost",
        "name": "myapp_db",
        "port": 5432
    },
    "debug": false,
    "version": "2.1.0"
}
```

Notice: keys are alphabetized (`allowed_origins` before `database`), `False` became `false`.

---

## 4. Reading JSON With Python

### Concept Overview

**Definition**
**Deserialization** is the reverse of serialization — converting a JSON string (text) back into a Python object. The `json` module's `loads()` and `load()` functions handle this.

**Mental Model**

```
JSON World (text)               Python World
┌──────────────┐  deserialize   ┌──────────────┐
│ JSON string  │ ─────────────→ │ dict, list,  │
│              │  json.loads()  │ int, str,    │
│              │                │ bool, None   │
└──────────────┘                └──────────────┘
```

### 2️⃣ Syntax & Structure

```python
import json

# loads() → parse a JSON STRING into Python (s = string)
python_obj = json.loads(json_string)

# load() → read JSON from a FILE into Python
python_obj = json.load(file_object)
```

**Type Conversion Table (JSON → Python):**

```
JSON Type          Python Type
──────────────────────────────
object        →    dict
array         →    list        (NOT tuple!)
string        →    str
number (int)  →    int
number (real) →    float
true          →    True
false         →    False
null          →    None
```

### 3️⃣ Example 1 — Foundational

**Goal:** Parse a JSON string into a Python dictionary and access its values.

```python
import json

# A JSON string (notice: this is a Python string containing JSON)
json_string = '{"name": "Alice", "age": 30, "is_active": true, "scores": [95, 87]}'

# Deserialize
data = json.loads(json_string)

# Now it's a regular Python dictionary
print(type(data))           # <class 'dict'>
print(data["name"])         # Alice
print(data["is_active"])    # True  (JSON true → Python True)
print(data["scores"][0])    # 95    (JSON array → Python list)
```

**Step-by-Step Runtime Walkthrough:**

1. `json_string` is a Python `str` containing valid JSON text.
2. `json.loads(json_string)` — The parser reads the string character by character:
   - `{` → start building a dict.
   - `"name"` → key (string).
   - `:` → separator.
   - `"Alice"` → value (string).
   - `true` → converted to Python `True`.
   - `[95, 87]` → converted to Python `list` (not tuple).
3. Returns a Python `dict`.
4. `data["scores"][0]` — Standard Python dict/list access. `data["scores"]` returns `[95, 87]`, then `[0]` gets `95`.

**Runtime Visualization:**

```
json.loads('{"name": "Alice", "age": 30, "is_active": true, "scores": [95, 87]}')
│
├─ Parse JSON text:
│   ├─ "name": "Alice"     → str: "Alice"
│   ├─ "age": 30           → int: 30
│   ├─ "is_active": true   → bool: True     (true → True)
│   └─ "scores": [95, 87]  → list: [95, 87] (array → list)
│
└─ Return: {"name": "Alice", "age": 30, "is_active": True, "scores": [95, 87]}
           ↑ This is now a Python dict, not a string
```

**Output:**

```
<class 'dict'>
Alice
True
95
```

---

### Open an External JSON File With Python

### 2️⃣ Syntax & Structure

```python
import json

with open("data.json", "r", encoding="utf-8") as file:
    data = json.load(file)
```

**Line-by-Line Breakdown:**

- `open("data.json", "r", encoding="utf-8")` — Opens the file in **read** mode with UTF-8 encoding.
- `json.load(file)` — Note: `load` (no 's'). Reads the entire file and parses the JSON into a Python object.

**`load` vs. `loads`:**

```
json.loads(string)    → parses a STRING     (s = string)
json.load(file)       → reads from a FILE   (no s = file)
```

### 4️⃣ Example 2 — Real-World / Interview Level

**Goal:** Build a robust configuration loader that reads JSON with error handling and default values.

```python
import json
from pathlib import Path

def load_config(filepath="config.json", defaults=None):
    """
    Load configuration from a JSON file with fallback defaults.

    Args:
        filepath: Path to the JSON config file.
        defaults: Dictionary of default values if file is missing or incomplete.

    Returns:
        Dictionary with configuration values.

    Raises:
        json.JSONDecodeError: If the file contains invalid JSON.
    """
    if defaults is None:
        defaults = {}

    path = Path(filepath)

    if not path.exists():
        print(f"Config file not found: {path}. Using defaults.")
        return defaults.copy()

    with open(path, "r", encoding="utf-8") as file:
        try:
            config = json.load(file)
        except json.JSONDecodeError as e:
            print(f"Invalid JSON in {path}: {e}")
            raise    # Re-raise so the caller knows something is wrong

    # Merge: config values override defaults
    merged = {**defaults, **config}
    return merged


# Usage
defaults = {
    "debug": False,
    "port": 8000,
    "host": "localhost",
    "log_level": "INFO"
}

config = load_config("config.json", defaults=defaults)
print(config)
```

**Deep Explanation:**

- `defaults=None` with `if defaults is None: defaults = {}` — This avoids the **mutable default argument** bug (covered in Week 3). If we wrote `defaults={}`, all calls would share the same dict.
- `path.exists()` — Checks if the file exists before trying to open it. Prevents `FileNotFoundError`.
- `json.JSONDecodeError` — Raised when the file contains invalid JSON (missing quotes, trailing commas, etc.). We catch it, print a helpful message, then re-raise.
- `{**defaults, **config}` — Dictionary unpacking merge. Keys from `config` override keys from `defaults`. If `config` has `"port": 9000`, it overrides the default `8000`.

**Runtime Flow Diagram:**

```
load_config("config.json", defaults={"debug": False, "port": 8000, ...})
│
├─ defaults is not None → use provided defaults
├─ path = Path("config.json")
├─ path.exists()? 
│   ├─ No → return defaults.copy()
│   └─ Yes → continue
│
├─ open("config.json", "r")
├─ json.load(file)
│   ├─ Valid JSON → config = {...}
│   └─ Invalid JSON → JSONDecodeError → print error → re-raise
│
├─ merged = {**defaults, **config}
│   Example:
│   defaults = {"debug": False, "port": 8000, "host": "localhost"}
│   config   = {"debug": True,  "port": 9000}
│   merged   = {"debug": True,  "port": 9000, "host": "localhost"}
│                ↑ overridden    ↑ overridden   ↑ kept from defaults
│
└─ return merged
```

---

## 5. Interacting With JSON

### Prettify JSON With Python

**Definition**
**Prettifying** (or "pretty-printing") means formatting JSON with indentation and newlines so humans can read it easily.

```python
import json

compact = '{"name":"Alice","scores":[95,87,92],"active":true}'

# Pretty-print to string
pretty = json.dumps(json.loads(compact), indent=2)
print(pretty)
```

**Step-by-Step:**

1. `json.loads(compact)` — Parse the compact JSON string into a Python dict.
2. `json.dumps(..., indent=2)` — Serialize it back to JSON with 2-space indentation.

**Output:**

```json
{
  "name": "Alice",
  "scores": [
    95,
    87,
    92
  ],
  "active": true
}
```

---

### Validate JSON in the Terminal

You can validate JSON without writing a Python script:

```bash
# Validate a JSON file (prints formatted output if valid, error if not)
python -m json.tool data.json

# Validate JSON from a string (pipe it in)
echo '{"name": "Alice"}' | python -m json.tool

# Invalid JSON shows an error
echo '{"name": "Alice",}' | python -m json.tool
# Error: Expecting property name enclosed in double quotes
```

**Explanation:**

- `python -m json.tool` — Runs the `json.tool` module as a command-line utility.
- If the JSON is valid, it prints it pretty-printed.
- If invalid, it shows the exact error and position.

---

### Pretty Print JSON in the Terminal

```bash
# Pretty-print a JSON file with sorted keys
python -m json.tool data.json --sort-keys

# Pretty-print with custom indentation
python -m json.tool data.json --indent 4
```

---

### Minify JSON With Python

**Definition**
**Minifying** is the opposite of prettifying — removing all unnecessary whitespace to make the JSON as compact as possible. This reduces file size for network transfer.

```python
import json

pretty_json = '''{
    "name": "Alice",
    "age": 30,
    "scores": [95, 87, 92]
}'''

# Minify: remove all unnecessary whitespace
minified = json.dumps(json.loads(pretty_json), separators=(",", ":"))
print(minified)
```

**Explanation:**

- `separators=(",", ":")` — By default, `json.dumps` uses `", "` (comma-space) and `": "` (colon-space). Setting separators to `(",", ":")` removes those spaces.

**Output:**

```
{"name":"Alice","age":30,"scores":[95,87,92]}
```

**Size Comparison:**

```
Pretty:   89 characters (with spaces and newlines)
Minified: 46 characters (no unnecessary whitespace)
Savings:  48% smaller
```

---

## 6. What Is a CSV File?

### Concept Overview

**Definition**
A **CSV** (Comma-Separated Values) file is a plain text file where each line represents a row of data, and values within each row are separated by commas. It's the simplest way to represent tabular (spreadsheet-like) data.

**Why It Exists**
Before JSON, before databases, people needed a way to move spreadsheet data between programs. CSV is so simple that *every* spreadsheet application, database, and programming language supports it. It's the "lowest common denominator" of data formats.

**Mental Model**
Think of a CSV file as a **spreadsheet saved as plain text**:

```
Spreadsheet View:                    CSV File (plain text):
┌──────┬─────┬────────┐
│ name │ age │ city   │             name,age,city
├──────┼─────┼────────┤             Alice,30,Portland
│Alice │ 30  │Portland│      →      Bob,25,Seattle
│Bob   │ 25  │Seattle │             Carol,35,Denver
│Carol │ 35  │Denver  │
└──────┴─────┴────────┘
```

**Where Do CSV Files Come From?**

- **Spreadsheet exports** — Excel, Google Sheets → "Save as CSV."
- **Database exports** — `SELECT * FROM users` → export to CSV.
- **APIs** — Some services provide data downloads as CSV.
- **Data science** — Datasets on Kaggle, government data portals.
- **Log files** — Some systems log events in CSV format.

**When To Use CSV**
- Tabular data (rows and columns).
- Data exchange between spreadsheet applications.
- Simple datasets without nested structures.

**When NOT To Use CSV**
- Nested or hierarchical data (use JSON).
- Data with commas in values (requires quoting, gets messy).
- When you need data types (CSV is all strings — no integers, booleans, etc.).
- Large-scale data processing (use Parquet or databases).

**Comparison: CSV vs. JSON**

| Feature | CSV | JSON |
|---|---|---|
| Structure | Flat (rows/columns) | Nested (objects/arrays) |
| Data types | Everything is text | Strings, numbers, booleans, null |
| Human readable | Very | Somewhat |
| File size | Smaller | Larger (due to key repetition) |
| Best for | Spreadsheet data | API responses, config |

---

## 7. Parsing CSV Files With Python's Built-in CSV Library

### Concept Overview

**Definition**
Python's built-in `csv` module provides tools to read and write CSV files. It handles edge cases like commas inside quoted fields, different delimiters, and line endings.

**Why Not Just Use `split(",")`?**

```python
# This SEEMS like it would work:
line = 'Alice,30,"Portland, OR"'
fields = line.split(",")
print(fields)
# ['Alice', '30', '"Portland', ' OR"']    ← WRONG! Split the city!

# The csv module handles this correctly:
import csv
import io
reader = csv.reader(io.StringIO(line))
print(next(reader))
# ['Alice', '30', 'Portland, OR']         ← CORRECT!
```

The `csv` module understands that commas inside quotes are part of the value, not separators.

### 2️⃣ Syntax & Structure

**Reading CSV — Two Approaches:**

```python
import csv

# Approach 1: csv.reader — returns lists
with open("data.csv", "r") as file:
    reader = csv.reader(file)
    for row in reader:
        print(row)    # Each row is a list: ['Alice', '30', 'Portland']

# Approach 2: csv.DictReader — returns dictionaries
with open("data.csv", "r") as file:
    reader = csv.DictReader(file)
    for row in reader:
        print(row)    # Each row is a dict: {'name': 'Alice', 'age': '30', ...}
```

### 3️⃣ Example 1 — Foundational

**Goal:** Read a CSV file using both `csv.reader` and `csv.DictReader`.

Assume this file exists:

```
name,age,city
Alice,30,Portland
Bob,25,Seattle
Carol,35,Denver
```

```python
import csv

# --- Using csv.reader ---
print("=== csv.reader ===")
with open("people.csv", "r") as file:
    reader = csv.reader(file)       # Create a reader object
    header = next(reader)           # Read the first row (header)
    print(f"Columns: {header}")

    for row in reader:              # Iterate remaining rows
        name = row[0]               # Access by index
        age = row[1]                # Everything is a STRING
        city = row[2]
        print(f"{name} is {age} years old, lives in {city}")
```

**Step-by-Step Runtime Walkthrough:**

1. `open("people.csv", "r")` — Opens the file for reading.
2. `csv.reader(file)` — Creates a reader object. It doesn't read anything yet — it's **lazy** (reads one row at a time).
3. `next(reader)` — Reads the first row: `["name", "age", "city"]`. This is the header.
4. `for row in reader` — Each iteration reads the next line and splits it by commas.
5. `row[0]`, `row[1]`, `row[2]` — Access values by position. **Important:** `row[1]` is `"30"` (a string), not `30` (an integer). CSV values are always strings.

**Runtime Visualization:**

```
csv.reader reads file line by line:

Line 1: "name,age,city"      → next(reader) → ["name", "age", "city"]
Line 2: "Alice,30,Portland"  → row = ["Alice", "30", "Portland"]
Line 3: "Bob,25,Seattle"     → row = ["Bob", "25", "Seattle"]
Line 4: "Carol,35,Denver"    → row = ["Carol", "35", "Denver"]
                                       ↑        ↑
                                    strings, not integers!
```

**Output:**

```
=== csv.reader ===
Columns: ['name', 'age', 'city']
Alice is 30 years old, lives in Portland
Bob is 25 years old, lives in Seattle
Carol is 35 years old, lives in Denver
```

---

### Reading CSV Files Into a Dictionary

```python
import csv

print("=== csv.DictReader ===")
with open("people.csv", "r") as file:
    reader = csv.DictReader(file)    # Automatically uses first row as keys

    for row in reader:
        # Access by column NAME instead of index
        print(f"{row['name']} is {row['age']}, lives in {row['city']}")
```

**Explanation:**

- `csv.DictReader(file)` — Reads the first row as column headers, then returns each subsequent row as a dictionary mapping header → value.
- `row['name']` — Much more readable than `row[0]`. If columns are reordered, your code still works.

**Runtime Visualization:**

```
DictReader automatically maps headers to values:

Header row: "name,age,city"  → keys = ["name", "age", "city"]

Line 2: "Alice,30,Portland"
  → {"name": "Alice", "age": "30", "city": "Portland"}

Line 3: "Bob,25,Seattle"
  → {"name": "Bob", "age": "25", "city": "Seattle"}
```

---

### Optional CSV Reader Parameters

```python
import csv

# Custom delimiter (tab-separated)
with open("data.tsv", "r") as file:
    reader = csv.reader(file, delimiter="\t")

# Custom quote character
with open("data.csv", "r") as file:
    reader = csv.reader(file, quotechar="'")

# Skip initial whitespace after delimiter
with open("data.csv", "r") as file:
    reader = csv.reader(file, skipinitialspace=True)
```

**Common Parameters:**

| Parameter | Default | Purpose |
|---|---|---|
| `delimiter` | `","` | Character that separates fields |
| `quotechar` | `'"'` | Character used to quote fields containing special chars |
| `skipinitialspace` | `False` | Ignore whitespace after delimiter |
| `lineterminator` | `"\r\n"` | Character(s) that end a row (writing only) |

---

## 8. Writing CSV Files With csv

### 2️⃣ Syntax & Structure

```python
import csv

# Writing with csv.writer (from lists)
with open("output.csv", "w", newline="") as file:
    writer = csv.writer(file)
    writer.writerow(["name", "age", "city"])       # Write one row
    writer.writerows([                              # Write multiple rows
        ["Alice", 30, "Portland"],
        ["Bob", 25, "Seattle"]
    ])
```

**Line-by-Line Breakdown:**

- `newline=""` — **Critical.** Without this, CSV files on Windows get extra blank lines between rows. The `csv` module handles line endings itself, so we tell `open()` not to add its own.
- `csv.writer(file)` — Creates a writer object.
- `writerow([...])` — Writes a single row (one list = one line).
- `writerows([[...], [...]])` — Writes multiple rows at once (list of lists).

---

### Writing CSV From a Dictionary

```python
import csv

users = [
    {"name": "Alice", "age": 30, "city": "Portland"},
    {"name": "Bob", "age": 25, "city": "Seattle"},
    {"name": "Carol", "age": 35, "city": "Denver"}
]

with open("users.csv", "w", newline="") as file:
    fieldnames = ["name", "age", "city"]           # Define column order
    writer = csv.DictWriter(file, fieldnames=fieldnames)

    writer.writeheader()                           # Write the header row
    writer.writerows(users)                        # Write all data rows
```

**Explanation:**

- `fieldnames` — A list defining which keys to include and in what order. If a dict has extra keys, they're ignored. If a key is missing, it raises `ValueError`.
- `writeheader()` — Writes the `fieldnames` as the first row.
- `writerows(users)` — Writes each dict as a row, extracting values in `fieldnames` order.

### 3️⃣ Example 1 — Foundational

**Goal:** Convert a list of dictionaries to a CSV file.

```python
import csv

students = [
    {"name": "Alice", "grade": "A", "score": 95},
    {"name": "Bob", "grade": "B", "score": 87},
    {"name": "Carol", "grade": "A", "score": 92}
]

with open("students.csv", "w", newline="") as file:
    fieldnames = ["name", "grade", "score"]
    writer = csv.DictWriter(file, fieldnames=fieldnames)

    writer.writeheader()
    writer.writerows(students)

# Verify by reading it back
with open("students.csv", "r") as file:
    print(file.read())
```

**Runtime Visualization:**

```
DictWriter processes each dict:

writeheader() → "name,grade,score\n"

writerows(students):
  {"name":"Alice","grade":"A","score":95}  → "Alice,A,95\n"
  {"name":"Bob","grade":"B","score":87}    → "Bob,B,87\n"
  {"name":"Carol","grade":"A","score":92}  → "Carol,A,92\n"

Final file:
  name,grade,score
  Alice,A,95
  Bob,B,87
  Carol,A,92
```

**Output:**

```
name,grade,score
Alice,A,95
Bob,B,87
Carol,A,92
```

### 4️⃣ Example 2 — Real-World / Interview Level

**Goal:** Build a data pipeline that reads JSON from an API response, transforms it, and writes to CSV.

```python
import json
import csv

# Simulated API response (JSON string)
api_response = '''
{
    "status": "success",
    "data": [
        {"id": 1, "name": "Alice", "email": "alice@example.com", "active": true},
        {"id": 2, "name": "Bob", "email": "bob@example.com", "active": false},
        {"id": 3, "name": "Carol", "email": "carol@example.com", "active": true}
    ]
}
'''

def json_to_csv(json_string, output_file, filter_active=False):
    """
    Convert JSON API response to a CSV file.

    Args:
        json_string: Raw JSON string from API.
        output_file: Path to output CSV file.
        filter_active: If True, only include active users.
    """
    # Step 1: Parse JSON
    parsed = json.loads(json_string)
    users = parsed["data"]                    # Extract the list

    # Step 2: Filter if requested
    if filter_active:
        users = [u for u in users if u["active"]]

    # Step 3: Write CSV
    if not users:
        print("No data to write.")
        return

    fieldnames = ["id", "name", "email"]      # Exclude 'active' from CSV
    with open(output_file, "w", newline="") as file:
        writer = csv.DictWriter(
            file,
            fieldnames=fieldnames,
            extrasaction="ignore"             # Ignore keys not in fieldnames
        )
        writer.writeheader()
        writer.writerows(users)

    print(f"Wrote {len(users)} records to {output_file}")


# Usage
json_to_csv(api_response, "all_users.csv")
json_to_csv(api_response, "active_users.csv", filter_active=True)
```

**Deep Explanation:**

- `parsed["data"]` — The API response has metadata (`"status"`) and the actual data. We extract just the list.
- `[u for u in users if u["active"]]` — List comprehension that filters to only active users. `u["active"]` is `True` or `False` (already converted from JSON `true`/`false` by `json.loads`).
- `extrasaction="ignore"` — Each user dict has an `"active"` key, but our `fieldnames` doesn't include it. Without `extrasaction="ignore"`, `DictWriter` would raise `ValueError`. This tells it to silently skip extra keys.

**Runtime Flow Diagram:**

```
json_to_csv(api_response, "active_users.csv", filter_active=True)
│
├─ json.loads(api_response)
│   └─ parsed = {"status": "success", "data": [...]}
│
├─ users = parsed["data"]  → 3 user dicts
│
├─ filter_active=True
│   └─ users = [u for u in users if u["active"]]
│       ├─ Alice: active=True  → KEEP
│       ├─ Bob:   active=False → SKIP
│       └─ Carol: active=True  → KEEP
│       Result: 2 users
│
├─ DictWriter(fieldnames=["id", "name", "email"], extrasaction="ignore")
│
├─ writeheader() → "id,name,email"
├─ writerows()
│   ├─ Alice → "1,Alice,alice@example.com"  (active key ignored)
│   └─ Carol → "3,Carol,carol@example.com"  (active key ignored)
│
└─ print("Wrote 2 records to active_users.csv")
```

**Output (active_users.csv):**

```
id,name,email
1,Alice,alice@example.com
3,Carol,carol@example.com
```

---

## 9. Parsing & Writing CSV Files With pandas

### Concept Overview

**Definition**
**pandas** is a third-party data analysis library that provides powerful tools for reading, manipulating, and writing tabular data. Its `read_csv()` and `to_csv()` functions are far more capable than the built-in `csv` module.

**Why Use pandas Over the csv Module?**

| Feature | `csv` module | `pandas` |
|---|---|---|
| Auto-detect types | No (everything is string) | Yes (int, float, datetime) |
| Handle missing data | Manual | Built-in (`NaN`) |
| Filter/sort/group | Manual loops | One-line methods |
| Large files | Slow | Optimized (C engine) |
| Data analysis | Not designed for it | Built for it |

**When To Use pandas**
- Data analysis, exploration, or transformation.
- Files with thousands+ rows.
- When you need type inference (auto-detect integers, dates, etc.).

**When To Use the csv Module**
- Simple read/write without analysis.
- When you can't install third-party packages.
- When memory is very limited (pandas loads entire file into memory).

### 2️⃣ Syntax & Structure

```python
import pandas as pd

# Reading
df = pd.read_csv("data.csv")          # Returns a DataFrame

# Writing
df.to_csv("output.csv", index=False)  # index=False prevents extra column
```

### 3️⃣ Example 1 — Foundational

**Goal:** Read a CSV file with pandas and explore the data.

```python
import pandas as pd

# Read CSV into a DataFrame
df = pd.read_csv("people.csv")

# Explore
print(df)                # Print entire table
print(df.dtypes)         # Show auto-detected types
print(df["age"].mean())  # Calculate average age
```

**Step-by-Step Runtime Walkthrough:**

1. `pd.read_csv("people.csv")` — pandas reads the file, auto-detects column types:
   - `name` → `object` (pandas term for string).
   - `age` → `int64` (detected as integer, not string!).
   - `city` → `object`.
2. `df` is a **DataFrame** — a 2D table with labeled rows and columns.
3. `df["age"]` — Selects the `age` column as a **Series** (1D array).
4. `.mean()` — Calculates the average. This works because pandas auto-detected `age` as numeric.

**Output:**

```
    name  age      city
0  Alice   30  Portland
1    Bob   25   Seattle
2  Carol   35    Denver

name    object
age      int64
city    object
dtype: object

30.0
```

### 4️⃣ Example 2 — Real-World / Interview Level

**Goal:** Read, filter, transform, and write CSV data using pandas.

```python
import pandas as pd

# Read sales data
df = pd.read_csv("sales.csv")

# Assume sales.csv contains:
# product,quantity,price,date
# Widget,10,29.99,2025-01-15
# Gadget,5,49.99,2025-01-15
# Widget,8,29.99,2025-01-16
# Gadget,12,49.99,2025-01-16

# Add a total column
df["total"] = df["quantity"] * df["price"]

# Filter: only rows where total > 200
high_value = df[df["total"] > 200]

# Group by product and sum
summary = df.groupby("product")["total"].sum().reset_index()
summary.columns = ["product", "revenue"]

# Sort by revenue descending
summary = summary.sort_values("revenue", ascending=False)

# Write to new CSV
summary.to_csv("revenue_summary.csv", index=False)
print(summary)
```

**Deep Explanation:**

- `df["total"] = df["quantity"] * df["price"]` — Creates a new column by multiplying two existing columns element-wise. pandas handles this without loops.
- `df[df["total"] > 200]` — Boolean filtering. `df["total"] > 200` creates a True/False series, and `df[...]` keeps only True rows.
- `groupby("product")["total"].sum()` — Groups rows by product name, then sums the `total` column within each group.
- `reset_index()` — After `groupby`, the product name becomes the index. This moves it back to a regular column.
- `index=False` — Without this, pandas writes the row numbers (0, 1, 2...) as an extra column.

**Output:**

```
  product  revenue
0  Gadget   849.83
1  Widget   539.82
```

---

## 10. Built-in Exceptions

### Concept Overview

**Definition**
Python has a hierarchy of **built-in exception classes** that represent different error conditions. Every error you've ever seen (`TypeError`, `ValueError`, `FileNotFoundError`) is an instance of one of these classes.

**Why It Exists**
Different errors need different handling. A missing file requires a different response than a type mismatch. Built-in exceptions provide a **standardized vocabulary** for errors, so code can catch and handle specific problems.

**Mental Model**
Think of exceptions as a **medical diagnosis system**. Instead of just saying "the patient is sick" (generic error), doctors classify illnesses into categories (flu, fracture, allergy). Each category has a specific treatment. Similarly, each exception type tells you exactly what went wrong and suggests how to fix it.

```
Generic:    "Something went wrong"          ← useless
Specific:   FileNotFoundError("config.json") ← actionable
```

**The Most Common Built-in Exceptions:**

| Exception | When It Occurs | Example |
|---|---|---|
| `TypeError` | Wrong type used | `"hello" + 5` |
| `ValueError` | Right type, wrong value | `int("abc")` |
| `KeyError` | Dict key not found | `d["missing"]` |
| `IndexError` | List index out of range | `[1,2,3][10]` |
| `AttributeError` | Object has no such attribute | `"hello".append("!")` |
| `FileNotFoundError` | File doesn't exist | `open("ghost.txt")` |
| `ZeroDivisionError` | Division by zero | `10 / 0` |
| `ImportError` | Module not found | `import nonexistent` |
| `NameError` | Variable not defined | `print(undefined_var)` |
| `StopIteration` | Iterator exhausted | `next(empty_iterator)` |

---

## 11. Exception Context

### Concept Overview

**Definition**
When one exception causes another, Python tracks the **chain** of exceptions. This is called **exception context**. It answers the question: "What was happening when this error occurred?"

### 2️⃣ Syntax & Structure

**Two Types of Chaining:**

```python
# Implicit chaining — happens automatically
try:
    d = {"key": "value"}
    result = d["missing"]          # KeyError
except KeyError:
    result = int("not_a_number")   # ValueError during handling!

# Python shows BOTH errors:
# KeyError: 'missing'
# During handling of the above exception, another exception occurred:
# ValueError: invalid literal for int()
```

```python
# Explicit chaining — you control it with "raise ... from ..."
try:
    value = int(input_string)
except ValueError as original:
    raise RuntimeError(f"Invalid config value: {input_string}") from original
```

**Line-by-Line Breakdown (Explicit Chaining):**

- `except ValueError as original` — Catches the `ValueError` and stores it in `original`.
- `raise RuntimeError(...) from original` — Raises a *new* exception (`RuntimeError`) and explicitly links it to the *original* cause (`ValueError`).
- The traceback will show both exceptions with "The above exception was the direct cause of the following exception."

**Why Use Explicit Chaining?**
- Provides a higher-level error message while preserving the original cause.
- Users see a meaningful error; developers can dig into the chain for debugging.

### 3️⃣ Example 1 — Foundational

**Goal:** Demonstrate implicit and explicit exception chaining.

```python
def load_user_age(data, key):
    """
    Extract and convert a user's age from a data dictionary.

    Args:
        data: Dictionary containing user data.
        key: The key to look up.

    Raises:
        RuntimeError: If the age cannot be loaded.
    """
    try:
        raw_value = data[key]          # Could raise KeyError
        age = int(raw_value)           # Could raise ValueError
        return age
    except (KeyError, ValueError) as e:
        raise RuntimeError(
            f"Failed to load age from key '{key}'"
        ) from e


# Test with missing key
try:
    load_user_age({"name": "Alice"}, "age")
except RuntimeError as e:
    print(f"Error: {e}")
    print(f"Caused by: {e.__cause__}")
```

**Step-by-Step Runtime Walkthrough:**

1. `data[key]` → `data["age"]` → `"age"` not in dict → `KeyError: 'age'`.
2. `except (KeyError, ValueError) as e` → catches the `KeyError`, stores in `e`.
3. `raise RuntimeError(...) from e` → creates a new `RuntimeError` and links it to the `KeyError`.
4. In the outer `try/except`, we catch the `RuntimeError`.
5. `e.__cause__` → the original `KeyError` that caused this chain.

**Output:**

```
Error: Failed to load age from key 'age'
Caused by: 'age'
```

**Suppressing Context:**

```python
# Use "from None" to hide the original exception
try:
    value = int("abc")
except ValueError:
    raise RuntimeError("Bad input") from None
    # Traceback shows ONLY RuntimeError, not the original ValueError
```

---

## 12. Inheriting From Built-in Exceptions

### Concept Overview

**Definition**
You can create your own exception classes by **inheriting** from Python's built-in exceptions. This lets you define application-specific errors that are more meaningful than generic ones.

**Why It Exists**
Imagine a banking application. `ValueError` could mean a hundred different things. But `InsufficientFundsError` tells you exactly what happened. Custom exceptions make error handling precise and self-documenting.

### 2️⃣ Syntax & Structure

```python
class CustomError(Exception):
    """Base exception for my application."""
    pass

class ValidationError(CustomError):
    """Raised when input validation fails."""
    def __init__(self, field, message):
        self.field = field
        self.message = message
        super().__init__(f"{field}: {message}")
```

**Line-by-Line Breakdown:**

- `class CustomError(Exception):` — Inherits from `Exception`. This is the standard base class for custom exceptions.
- `pass` — No additional behavior needed; it's just a named exception.
- `class ValidationError(CustomError):` — Inherits from *our* base, not directly from `Exception`. This creates a hierarchy.
- `def __init__(self, field, message):` — Custom constructor that accepts structured data.
- `self.field = field` — Store the field name as an attribute. Code catching this exception can access `e.field`.
- `super().__init__(...)` — Call the parent's `__init__` to set the standard error message.

### 3️⃣ Example 1 — Foundational

**Goal:** Create a custom exception hierarchy for a user registration system.

```python
# exceptions.py

class AppError(Exception):
    """Base exception for the application."""
    pass

class ValidationError(AppError):
    """Raised when input validation fails."""
    def __init__(self, field, message):
        self.field = field
        self.message = message
        super().__init__(f"Validation failed for '{field}': {message}")

class AuthenticationError(AppError):
    """Raised when authentication fails."""
    pass
```

```python
# auth.py

from exceptions import ValidationError, AuthenticationError

def register_user(username, password):
    """Register a new user with validation."""
    if len(username) < 3:
        raise ValidationError("username", "Must be at least 3 characters")

    if len(password) < 8:
        raise ValidationError("password", "Must be at least 8 characters")

    # Simulate registration
    return {"username": username, "registered": True}


# Usage with targeted exception handling
try:
    result = register_user("ab", "short")
except ValidationError as e:
    print(f"Field: {e.field}")        # Access structured data
    print(f"Issue: {e.message}")
    print(f"Full:  {e}")
```

**Runtime Visualization:**

```
register_user("ab", "short")
│
├─ len("ab") = 2
├─ 2 < 3? → True
│
└─ raise ValidationError("username", "Must be at least 3 characters")
    ├─ self.field = "username"
    ├─ self.message = "Must be at least 3 characters"
    └─ super().__init__("Validation failed for 'username': Must be at least 3 characters")

except ValidationError as e:
├─ e.field   → "username"
├─ e.message → "Must be at least 3 characters"
└─ str(e)    → "Validation failed for 'username': Must be at least 3 characters"
```

**Output:**

```
Field: username
Issue: Must be at least 3 characters
Full:  Validation failed for 'username': Must be at least 3 characters
```

### 4️⃣ Example 2 — Real-World / Interview Level

**Goal:** Build a complete exception hierarchy for an e-commerce system with structured error data.

```python
# exceptions.py

class ECommerceError(Exception):
    """Base exception for the e-commerce platform."""
    def __init__(self, message, code=None):
        self.code = code
        super().__init__(message)

class ProductError(ECommerceError):
    """Errors related to products."""
    pass

class ProductNotFoundError(ProductError):
    """Raised when a product ID doesn't exist."""
    def __init__(self, product_id):
        self.product_id = product_id
        super().__init__(
            f"Product not found: {product_id}",
            code="PRODUCT_NOT_FOUND"
        )

class InsufficientStockError(ProductError):
    """Raised when requested quantity exceeds available stock."""
    def __init__(self, product_id, requested, available):
        self.product_id = product_id
        self.requested = requested
        self.available = available
        super().__init__(
            f"Insufficient stock for {product_id}: "
            f"requested {requested}, available {available}",
            code="INSUFFICIENT_STOCK"
        )

class PaymentError(ECommerceError):
    """Errors related to payment processing."""
    pass

class PaymentDeclinedError(PaymentError):
    """Raised when a payment is declined."""
    def __init__(self, reason):
        self.reason = reason
        super().__init__(
            f"Payment declined: {reason}",
            code="PAYMENT_DECLINED"
        )
```

```python
# cart.py

from exceptions import (
    ProductNotFoundError,
    InsufficientStockError,
    PaymentDeclinedError,
    ProductError,
    ECommerceError
)

# Simulated product database
PRODUCTS = {
    "SKU001": {"name": "Widget", "price": 29.99, "stock": 5},
    "SKU002": {"name": "Gadget", "price": 49.99, "stock": 0},
}

def add_to_cart(product_id, quantity):
    """Add a product to the shopping cart."""
    if product_id not in PRODUCTS:
        raise ProductNotFoundError(product_id)

    product = PRODUCTS[product_id]
    if quantity > product["stock"]:
        raise InsufficientStockError(
            product_id, quantity, product["stock"]
        )

    return {"product": product["name"], "quantity": quantity}


# Handling at different levels of specificity
def process_order(product_id, quantity):
    """Demonstrate hierarchical exception handling."""
    try:
        item = add_to_cart(product_id, quantity)
        print(f"Added: {item}")
    except InsufficientStockError as e:
        # Handle specific case: suggest lower quantity
        print(f"Only {e.available} available. Reduce quantity?")
    except ProductError as e:
        # Handle any product-related error
        print(f"Product issue [{e.code}]: {e}")
    except ECommerceError as e:
        # Handle any application error
        print(f"Application error [{e.code}]: {e}")


# Test cases
process_order("SKU001", 3)     # Success
process_order("SKU002", 1)     # Insufficient stock
process_order("SKU999", 1)     # Product not found
```

**Deep Explanation — Why Order Matters:**

The `except` blocks are ordered from **most specific to least specific**. Python checks them top-to-bottom and uses the first match:

```
Exception Hierarchy:
    ECommerceError
    ├── ProductError
    │   ├── ProductNotFoundError
    │   └── InsufficientStockError
    └── PaymentError

        └── PaymentDeclinedError

except InsufficientStockError:   ← catches ONLY InsufficientStockError
except ProductError:             ← catches ProductNotFoundError too (parent class)
except ECommerceError:           ← catches EVERYTHING (grandparent)

WRONG ORDER (catches too broadly first):
except ECommerceError:           ← catches EVERYTHING — specific handlers never run!
except InsufficientStockError:   ← UNREACHABLE
```

**Runtime Flow Diagram (for `process_order("SKU999", 1)`):**

```
process_order("SKU999", 1)
│
├─ add_to_cart("SKU999", 1)
│   ├─ "SKU999" not in PRODUCTS? → True
│   └─ raise ProductNotFoundError("SKU999")
│       ├─ self.product_id = "SKU999"
│       └─ super().__init__("Product not found: SKU999", code="PRODUCT_NOT_FOUND")
│
├─ except InsufficientStockError?
│   └─ ProductNotFoundError is NOT InsufficientStockError → SKIP
│
├─ except ProductError?
│   └─ ProductNotFoundError IS a ProductError (inheritance) → MATCH ✅
│   └─ print("Product issue [PRODUCT_NOT_FOUND]: Product not found: SKU999")
│
└─ except ECommerceError? → never reached (already handled above)
```

**Output:**

```
Added: {'product': 'Widget', 'quantity': 3}
Only 0 available. Reduce quantity?
Product issue [PRODUCT_NOT_FOUND]: Product not found: SKU999
```

---

## 13. Base Classes & Concrete Exceptions

### Concept Overview

**Definition**
Python's exception system is organized into **base classes** (abstract categories) and **concrete exceptions** (specific errors you actually encounter). Understanding this hierarchy helps you write precise `except` clauses.

**Base Exception Classes:**

```
BaseException                  ← Root of ALL exceptions
├── SystemExit                 ← raised by sys.exit()
├── KeyboardInterrupt          ← raised by Ctrl+C
├── GeneratorExit              ← raised when generator is closed
└── Exception                  ← Root of all "normal" exceptions
    ├── ArithmeticError        ← Base for math errors
    │   ├── ZeroDivisionError
    │   ├── OverflowError
    │   └── FloatingPointError
    ├── LookupError            ← Base for lookup errors
    │   ├── IndexError
    │   └── KeyError
    ├── OSError                ← Base for OS-related errors
    │   ├── FileNotFoundError
    │   ├── PermissionError
    │   └── ConnectionError
    └── ValueError, TypeError, etc.
```

**Why This Matters:**

```python
# Catching a BASE class catches ALL its children:

try:
    result = my_list[100]      # IndexError
except LookupError:            # Catches IndexError AND KeyError
    print("Lookup failed")

# This is useful when you don't care about the specific error:
try:
    value = data[key]          # Could be dict (KeyError) or list (IndexError)
except LookupError:
    print("Item not found")    # Handles both!
```

**Critical Rule: Never catch `BaseException`**

```python
# WRONG — catches Ctrl+C and sys.exit(), making your program unkillable
try:
    do_something()
except BaseException:          # ← NEVER do this
    pass

# RIGHT — catches all "normal" errors
try:
    do_something()
except Exception:              # ← This is the broadest you should go
    pass
```

**Why?** `BaseException` includes `KeyboardInterrupt` (Ctrl+C) and `SystemExit` (sys.exit()). Catching these prevents users from stopping your program and prevents clean shutdowns.

### 2️⃣ Syntax & Structure

**Common Concrete Exceptions and When They Occur:**

```python
# ArithmeticError family
10 / 0                          # ZeroDivisionError
10 ** 10000000                  # OverflowError (in some contexts)

# LookupError family
[1, 2, 3][10]                  # IndexError
{"a": 1}["b"]                  # KeyError

# OSError family
open("nonexistent.txt")        # FileNotFoundError
open("/root/secret.txt")       # PermissionError
import socket; socket.connect(("bad.host", 80))  # ConnectionError

# ValueError — right type, wrong value
int("hello")                   # ValueError
list.remove("missing_item")    # ValueError

# TypeError — wrong type entirely
"hello" + 5                    # TypeError
len(42)                        # TypeError

# AttributeError — object doesn't have that attribute
"hello".nonexistent_method()   # AttributeError

# NameError — variable doesn't exist
print(undefined_variable)      # NameError
```

### 3️⃣ Example 1 — Foundational

**Goal:** Demonstrate catching at different levels of the hierarchy.

```python
def safe_lookup(collection, key):
    """
    Safely look up a value in any collection type.
    Works with both dicts (KeyError) and lists (IndexError).
    """
    try:
        return collection[key]
    except LookupError as e:
        # LookupError catches both KeyError and IndexError
        error_type = type(e).__name__
        print(f"{error_type}: {e}")
        return None


# Works with dictionaries
result = safe_lookup({"name": "Alice"}, "age")
print(f"Dict result: {result}")

# Works with lists
result = safe_lookup([10, 20, 30], 99)
print(f"List result: {result}")
```

**Step-by-Step Runtime Walkthrough:**

1. `safe_lookup({"name": "Alice"}, "age")`:
   - `collection["age"]` → key `"age"` not in dict → `KeyError: 'age'`.
   - `except LookupError` → `KeyError` is a subclass of `LookupError` → caught.
   - `type(e).__name__` → `"KeyError"`.
   - Returns `None`.

2. `safe_lookup([10, 20, 30], 99)`:
   - `collection[99]` → index 99 out of range → `IndexError`.
   - `except LookupError` → `IndexError` is a subclass of `LookupError` → caught.
   - Returns `None`.

**Output:**

```
KeyError: 'age'
Dict result: None
IndexError: list index out of range
List result: None
```

---

## 14. OS Exceptions & Warnings

### Concept Overview

**Definition**
**OS exceptions** are errors raised by the operating system through Python. They all inherit from `OSError` and represent problems with files, permissions, network connections, and other system resources.

**The OSError Family:**

```
OSError
├── FileNotFoundError      ← File or directory doesn't exist
├── FileExistsError        ← File already exists (when creating)
├── PermissionError        ← No permission to access
├── IsADirectoryError      ← Expected file, got directory
├── NotADirectoryError     ← Expected directory, got file
├── ConnectionError        ← Network connection problems
│   ├── ConnectionRefusedError
│   ├── ConnectionResetError
│   └── ConnectionAbortedError
├── TimeoutError           ← Operation timed out
└── InterruptedError       ← System call interrupted
```

### 2️⃣ Syntax & Structure

```python
import os
from pathlib import Path

def safe_read_file(filepath):
    """Read a file with comprehensive OS error handling."""
    path = Path(filepath)
    try:
        return path.read_text(encoding="utf-8")
    except FileNotFoundError:
        print(f"File not found: {path}")
    except PermissionError:
        print(f"No permission to read: {path}")
    except IsADirectoryError:
        print(f"Expected a file, got a directory: {path}")
    except OSError as e:
        # Catch any other OS-related error
        print(f"OS error reading {path}: {e}")
    return None
```

---

### Warnings

**Definition**
**Warnings** are not exceptions — they don't stop your program. They alert you to potential issues (deprecated features, risky operations) while allowing execution to continue.

```python
import warnings

# Issue a warning
warnings.warn("This function is deprecated. Use new_function() instead.",
              DeprecationWarning)

# Control warning behavior
warnings.filterwarnings("ignore", category=DeprecationWarning)   # Suppress
warnings.filterwarnings("error", category=DeprecationWarning)    # Treat as error
```

**Warning Categories:**

| Warning | Purpose |
|---|---|
| `DeprecationWarning` | Feature will be removed in future versions |
| `FutureWarning` | Behavior will change in future versions |
| `RuntimeWarning` | Suspicious runtime behavior |
| `UserWarning` | Generic user-defined warning (default) |
| `SyntaxWarning` | Suspicious syntax |
| `ResourceWarning` | Resource usage issues (unclosed files) |

**Key Difference: Warnings vs. Exceptions**

```
Exception:  Program STOPS (unless caught)
Warning:    Program CONTINUES (just prints a message)

# Exception
raise ValueError("bad input")     # Program crashes here

# Warning
warnings.warn("suspicious input") # Program prints warning, keeps going
print("This still runs!")         # ← This executes
```

### 3️⃣ Example 1 — Foundational

**Goal:** Create a function that uses warnings for deprecation.

```python
import warnings

def old_calculate(x, y):
    """Deprecated: Use calculate() instead."""
    warnings.warn(
        "old_calculate() is deprecated. Use calculate() instead.",
        DeprecationWarning,
        stacklevel=2    # Point warning at the CALLER, not this function
    )
    return calculate(x, y)

def calculate(x, y):
    """The current calculation function."""
    return x + y

# Usage
result = old_calculate(5, 3)    # Prints warning but still returns 8
print(f"Result: {result}")
```

**Explanation:**

- `stacklevel=2` — Without this, the warning points to the line inside `old_calculate`. With `stacklevel=2`, it points to the line where the *caller* invoked `old_calculate` — much more useful for debugging.

**Output:**

```
/path/to/script.py:15: DeprecationWarning: old_calculate() is deprecated. Use calculate() instead.
  result = old_calculate(5, 3)
Result: 8
```

Notice: the warning shows the *caller's* line (`result = old_calculate(5, 3)`), not the `warnings.warn()` line. That's what `stacklevel=2` does.

---

## 15. Exception Groups

### Concept Overview

**Definition**
**Exception Groups** (introduced in Python 3.11) allow you to raise and handle **multiple exceptions simultaneously**. Before this, you could only raise one exception at a time.

**Why It Exists**
Some operations can fail in multiple ways at once. For example:
- Validating a form: username is too short AND email is invalid AND password is weak.
- Running concurrent tasks: task A fails with `TimeoutError` AND task B fails with `ValueError`.

Without exception groups, you'd have to stop at the first error or collect errors manually. Exception groups let you raise all errors together.

**Mental Model**

```
Traditional Exception:           Exception Group:
    raise ValueError("bad")         raise ExceptionGroup("validation", [
                                        ValueError("name too short"),
    Only ONE error at a time            TypeError("age must be int"),
                                        ValueError("email invalid")
                                    ])
                                    MULTIPLE errors at once
```

### 2️⃣ Syntax & Structure

```python
# Creating an ExceptionGroup
raise ExceptionGroup("validation errors", [
    ValueError("name too short"),
    TypeError("age must be integer"),
    ValueError("email invalid")
])

# Handling with except* (note the asterisk!)
try:
    risky_operation()
except* ValueError as eg:       # Catches all ValueErrors in the group
    for e in eg.exceptions:
        print(f"ValueError: {e}")
except* TypeError as eg:        # Catches all TypeErrors in the group
    for e in eg.exceptions:
        print(f"TypeError: {e}")
```

**Key Differences from Regular except:**

| Feature | `except` | `except*` |
|---|---|---|
| Catches | One exception | A group of exceptions |
| Variable type | Single exception | `ExceptionGroup` containing matches |
| Multiple handlers | First match wins | ALL matching handlers run |
| Access errors | `e` directly | `eg.exceptions` (tuple) |

### 3️⃣ Example 1 — Foundational

**Goal:** Validate multiple fields and report all errors at once.

```python
def validate_user_data(data):
    """
    Validate all fields and report ALL errors, not just the first one.

    Args:
        data: Dictionary with 'name', 'age', 'email' keys.

    Raises:
        ExceptionGroup: If any validation fails.
    """
    errors = []

    # Check name
    if not isinstance(data.get("name"), str) or len(data["name"]) < 2:
        errors.append(ValueError("Name must be a string with 2+ characters"))

    # Check age
    if not isinstance(data.get("age"), int):
        errors.append(TypeError("Age must be an integer"))
    elif data["age"] < 0 or data["age"] > 150:
        errors.append(ValueError("Age must be between 0 and 150"))

    # Check email
    email = data.get("email", "")
    if not isinstance(email, str) or "@" not in email:
        errors.append(ValueError("Email must contain '@'"))

    # If any errors, raise them ALL at once
    if errors:
        raise ExceptionGroup("User validation failed", errors)

    return True


# Usage
try:
    validate_user_data({"name": "A", "age": "thirty", "email": "bad"})
except* ValueError as eg:
    print(f"Value errors ({len(eg.exceptions)}):")
    for e in eg.exceptions:
        print(f"  - {e}")
except* TypeError as eg:
    print(f"Type errors ({len(eg.exceptions)}):")
    for e in eg.exceptions:
        print(f"  - {e}")
```

**Step-by-Step Runtime Walkthrough:**

1. `validate_user_data({"name": "A", "age": "thirty", "email": "bad"})`:
   - Name check: `len("A") = 1`, `1 < 2` → append `ValueError("Name must be...")`.
   - Age check: `isinstance("thirty", int)` → `False` → append `TypeError("Age must be...")`.
   - Email check: `"@" not in "bad"` → `True` → append `ValueError("Email must contain '@'")`.
   - `errors` has 3 items → raise `ExceptionGroup`.

2. `except* ValueError as eg`:
   - Python filters the group: finds 2 `ValueError`s (name and email).
   - `eg.exceptions` = `(ValueError("Name..."), ValueError("Email..."))`.
   - Prints both.

3. `except* TypeError as eg`:
   - Python filters the group: finds 1 `TypeError` (age).
   - `eg.exceptions` = `(TypeError("Age..."),)`.
   - Prints it.

**Both handlers run** — unlike regular `except` where only the first match executes.

**Runtime Visualization:**

```
ExceptionGroup("User validation failed", [
    ValueError("Name must be a string with 2+ characters"),   ─┐
    TypeError("Age must be an integer"),                       ─┤
    ValueError("Email must contain '@'")                       ─┘
])                                                              │
                                                                ↓
except* ValueError:  ← matches 2 errors (Name + Email)    → RUNS ✅
except* TypeError:   ← matches 1 error (Age)              → RUNS ✅
                       (BOTH handlers execute!)
```

**Output:**

```
Value errors (2):
  - Name must be a string with 2+ characters
  - Email must contain '@'
Type errors (1):
  - Age must be an integer
```

### 4️⃣ Example 2 — Real-World / Interview Level

**Goal:** Use exception groups in a concurrent task runner that collects errors from multiple operations.

```python
def process_batch(items):
    """
    Process multiple items, collecting all failures.

    Instead of stopping at the first error, processes everything
    and reports all failures at the end.
    """
    results = []
    errors = []

    for i, item in enumerate(items):
        try:
            # Simulate processing that might fail
            if not isinstance(item, (int, float)):
                raise TypeError(f"Item {i}: expected number, got {type(item).__name__}")
            if item < 0:
                raise ValueError(f"Item {i}: negative values not allowed ({item})")
            if item == 0:
                raise ZeroDivisionError(f"Item {i}: zero not allowed")
            results.append(item * 2)    # Simple transformation
        except (TypeError, ValueError, ZeroDivisionError) as e:
            errors.append(e)            # Collect error, continue processing

    if errors:
        raise ExceptionGroup(
            f"Batch processing failed ({len(errors)}/{len(items)} items)",
            errors
        )

    return results


# Usage
items = [10, -5, "hello", 0, 20, None, 30]

try:
    results = process_batch(items)
except* TypeError as eg:
    print("Type errors:")
    for e in eg.exceptions:
        print(f"  {e}")
except* ValueError as eg:
    print("Value errors:")
    for e in eg.exceptions:
        print(f"  {e}")
except* ZeroDivisionError as eg:
    print("Zero errors:")
    for e in eg.exceptions:
        print(f"  {e}")
```

**Deep Explanation:**

- The function processes ALL items, even when some fail. Errors are collected in a list.
- After processing everything, if there were any errors, they're raised as a group.
- The caller can handle different error types separately with `except*`.
- This pattern is common in **batch processing**, **data pipelines**, and **form validation**.

**Runtime Flow Diagram:**

```
process_batch([10, -5, "hello", 0, 20, None, 30])
│
├─ Item 0: 10     → 10 * 2 = 20        → results: [20]
├─ Item 1: -5     → negative!          → errors: [ValueError]
├─ Item 2: "hello"→ not a number!      → errors: [ValueError, TypeError]
├─ Item 3: 0      → zero!              → errors: [ValueError, TypeError, ZeroDivision]
├─ Item 4: 20     → 20 * 2 = 40        → results: [20, 40]
├─ Item 5: None   → not a number!      → errors: [..., TypeError]
├─ Item 6: 30     → 30 * 2 = 60        → results: [20, 40, 60]
│
├─ 4 errors collected → raise ExceptionGroup
│
├─ except* TypeError:        matches 2 (items 2, 5)
├─ except* ValueError:       matches 1 (item 1)
└─ except* ZeroDivisionError: matches 1 (item 3)
```

**Output:**

```
Type errors:
  Item 2: expected number, got str
  Item 5: expected number, got NoneType
Value errors:
  Item 1: negative values not allowed (-5)
Zero errors:
  Item 3: zero not allowed
```

---

## 16. Exception Hierarchy

### Concept Overview

**Definition**
Python's complete exception hierarchy is a tree of classes where every exception inherits from `BaseException`. Understanding this tree is essential for writing correct `except` clauses.

**The Complete Hierarchy (Most Important Parts):**

```
BaseException
├── BaseExceptionGroup          (Python 3.11+)
├── SystemExit                  sys.exit() calls this
├── KeyboardInterrupt           Ctrl+C
├── GeneratorExit               Generator cleanup
│
└── Exception                   ← YOUR except clauses should start here
    │
    ├── ExceptionGroup          (Python 3.11+)
    │
    ├── StopIteration           Iterator exhausted
    ├── StopAsyncIteration      Async iterator exhausted
    │
    ├── ArithmeticError         Math problems
    │   ├── ZeroDivisionError
    │   ├── OverflowError
    │   └── FloatingPointError
    │
    ├── AssertionError          assert statement failed
    │
    ├── AttributeError          obj.nonexistent
    │
    ├── BufferError              Buffer operations
    │
    ├── EOFError                 input() hits end-of-file
    │
    ├── ImportError              Import failed
    │   └── ModuleNotFoundError
    │
    ├── LookupError             Key/index problems
    │   ├── IndexError
    │   └── KeyError
    │
    ├── MemoryError              Out of memory
    │
    ├── NameError                Undefined variable
    │   └── UnboundLocalError
    │
    ├── OSError                  Operating system errors
    │   ├── FileNotFoundError
    │   ├── FileExistsError
    │   ├── PermissionError
    │   ├── IsADirectoryError
    │   ├── NotADirectoryError
    │   ├── ConnectionError
    │   │   ├── ConnectionRefusedError
    │   │   ├── ConnectionResetError
    │   │   └── ConnectionAbortedError
    │   ├── TimeoutError
    │   └── InterruptedError
    │
    ├── RuntimeError             Generic runtime error
    │   ├── NotImplementedError
    │   └── RecursionError
    │
    ├── SyntaxError              Invalid Python syntax
    │   └── IndentationError
    │       └── TabError
    │
    ├── TypeError                Wrong type
    │
    ├── ValueError               Right type, wrong value
    │   └── UnicodeError
    │       ├── UnicodeDecodeError
    │       ├── UnicodeEncodeError
    │       └── UnicodeTranslateError
    │
    └── Warning                  Base for all warnings
        ├── DeprecationWarning
        ├── FutureWarning
        ├── RuntimeWarning
        ├── UserWarning
        ├── SyntaxWarning
        └── ResourceWarning
```

**Key Rules for Using the Hierarchy:**

**Rule 1: Catch specific exceptions first, general ones last.**

```python
# CORRECT order (specific → general)
try:
    operation()
except FileNotFoundError:     # Most specific
    handle_missing_file()
except OSError:               # Parent class
    handle_os_error()
except Exception:             # Broadest (last resort)
    handle_unknown()
```

**Rule 2: Never catch `BaseException` unless you have a very specific reason.**

```python
# WRONG — prevents Ctrl+C from working
except BaseException:
    pass

# RIGHT — catches all "normal" errors
except Exception:
    pass
```

**Rule 3: Use parent classes when you want to catch a category.**

```python
# Instead of listing every OS error:
except (FileNotFoundError, PermissionError, IsADirectoryError, ...):

# Catch the parent:
except OSError:    # Catches ALL OS-related errors
```

### 3️⃣ Example 1 — Foundational

**Goal:** Demonstrate how inheritance affects exception catching.

```python
def demonstrate_hierarchy():
    """Show how parent classes catch child exceptions."""

    errors_to_test = [
        ("ZeroDivisionError", lambda: 1 / 0),
        ("IndexError",        lambda: [1, 2][10]),
        ("KeyError",          lambda: {}["missing"]),
        ("FileNotFoundError", lambda: open("ghost.txt")),
        ("ValueError",        lambda: int("abc")),
    ]

    for name, trigger in errors_to_test:
        try:
            trigger()
        except ArithmeticError:
            print(f"{name:25s} → caught by ArithmeticError")
        except LookupError:
            print(f"{name:25s} → caught by LookupError")
        except OSError:
            print(f"{name:25s} → caught by OSError")
        except Exception:
            print(f"{name:25s} → caught by Exception (fallback)")

demonstrate_hierarchy()
```

**Step-by-Step Runtime Walkthrough:**

1. `1 / 0` → `ZeroDivisionError` → is it `ArithmeticError`? Yes (child class) → caught.
2. `[1,2][10]` → `IndexError` → is it `ArithmeticError`? No → is it `LookupError`? Yes → caught.
3. `{}["missing"]` → `KeyError` → is it `ArithmeticError`? No → is it `LookupError`? Yes → caught.
4. `open("ghost.txt")` → `FileNotFoundError` → ArithmeticError? No → LookupError? No → OSError? Yes → caught.
5. `int("abc")` → `ValueError` → ArithmeticError? No → LookupError? No → OSError? No → Exception? Yes → caught.

**Output:**

```
ZeroDivisionError         → caught by ArithmeticError
IndexError                → caught by LookupError
KeyError                  → caught by LookupError
FileNotFoundError         → caught by OSError
ValueError                → caught by Exception (fallback)
```

### 4️⃣ Example 2 — Real-World / Interview Level

**Goal:** Build a robust error handler that logs errors differently based on their position in the hierarchy.

```python
import traceback
from datetime import datetime

def categorize_and_handle(func, *args, **kwargs):
    """
    Execute a function and handle errors based on their category
    in the exception hierarchy.

    Returns:
        tuple: (success: bool, result_or_error: any)
    """
    try:
        result = func(*args, **kwargs)
        return (True, result)

    except KeyboardInterrupt:
        # NEVER silently catch this — always re-raise
        print("User interrupted. Shutting down gracefully...")
        raise

    except (ConnectionError, TimeoutError) as e:
        # Network errors — might be temporary, suggest retry
        print(f"[NETWORK] {type(e).__name__}: {e}")
        print("  → This might be temporary. Consider retrying.")
        return (False, e)

    except OSError as e:
        # File/system errors — likely a configuration issue
        print(f"[SYSTEM] {type(e).__name__}: {e}")
        print("  → Check file paths and permissions.")
        return (False, e)

    except (TypeError, ValueError) as e:
        # Input errors — caller's fault
        print(f"[INPUT] {type(e).__name__}: {e}")
        print("  → Check the arguments you're passing.")
        return (False, e)

    except LookupError as e:
        # Missing data — key or index not found
        print(f"[DATA] {type(e).__name__}: {e}")
        print("  → The requested item doesn't exist.")
        return (False, e)

    except Exception as e:
        # Unknown error — log everything for debugging
        print(f"[UNKNOWN] {type(e).__name__}: {e}")
        print(f"  → Full traceback:\n{traceback.format_exc()}")
        return (False, e)


# Test with different error types
print("--- Test 1: File error ---")
categorize_and_handle(open, "nonexistent.txt")

print("\n--- Test 2: Type error ---")
categorize_and_handle(int, [1, 2, 3])

print("\n--- Test 3: Lookup error ---")
categorize_and_handle(lambda: {"a": 1}["b"])

print("\n--- Test 4: Success ---")
success, result = categorize_and_handle(int, "42")
print(f"Success: {success}, Result: {result}")
```

**Deep Explanation:**

- **Order matters critically.** `ConnectionError` and `TimeoutError` are checked *before* `OSError` because they're subclasses of `OSError`. If `OSError` came first, network errors would be misclassified as "file/system" errors.
- **`KeyboardInterrupt` is re-raised.** We print a message but always re-raise so the program actually stops. Swallowing `KeyboardInterrupt` makes programs unkillable.
- **The `Exception` fallback** catches anything we didn't anticipate. It logs the full traceback for debugging.
- **Each category gets different advice.** Network errors suggest retrying; file errors suggest checking paths; input errors blame the caller.

**Runtime Flow Diagram (for Test 1):**

```
categorize_and_handle(open, "nonexistent.txt")
│
├─ func = open, args = ("nonexistent.txt",)
├─ open("nonexistent.txt") → FileNotFoundError
│
├─ except KeyboardInterrupt? → No
├─ except (ConnectionError, TimeoutError)? → No
├─ except OSError?
│   └─ FileNotFoundError IS an OSError → MATCH ✅
│   └─ print("[SYSTEM] FileNotFoundError: ...")
│
└─ return (False, FileNotFoundError(...))
```

**Output:**

```
--- Test 1: File error ---
[SYSTEM] FileNotFoundError: [Errno 2] No such file or directory: 'nonexistent.txt'
  → Check file paths and permissions.

--- Test 2: Type error ---
[INPUT] TypeError: int() argument must be a string, a bytes-like object or a real number, not 'list'
  → Check the arguments you're passing.

--- Test 3: Lookup error ---
[DATA] KeyError: 'b'
  → The requested item doesn't exist.

--- Test 4: Success ---
Success: True, Result: 42
```

---

## Quick Reference Card

```
WEEK 6 CHEAT SHEET
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

JSON — WRITING:
  import json
  json.dumps(obj)              → JSON string
  json.dumps(obj, indent=2)    → pretty string
  json.dump(obj, file)         → write to file

JSON — READING:
  json.loads(string)           → Python object
  json.load(file)              → read from file

JSON — TOOLS:
  python -m json.tool f.json   → validate & pretty-print
  json.dumps(obj, separators=(",",":"))  → minify

JSON ↔ PYTHON TYPE MAP:
  dict ↔ object    list ↔ array     str ↔ string
  int/float ↔ number   True/False ↔ true/false
  None ↔ null      tuple → array (one-way!)

CSV — READING:
  import csv
  csv.reader(file)             → rows as lists
  csv.DictReader(file)         → rows as dicts

CSV — WRITING:
  csv.writer(file)             → write lists
  csv.DictWriter(file, fieldnames=[...])  → write dicts
  Always use: open(..., newline="")

CSV — PANDAS:
  import pandas as pd
  df = pd.read_csv("file.csv")
  df.to_csv("out.csv", index=False)

EXCEPTIONS — HIERARCHY:
  BaseException → SystemExit, KeyboardInterrupt
  Exception → all normal errors
    ArithmeticError → ZeroDivisionError
    LookupError → IndexError, KeyError
    OSError → FileNotFoundError, PermissionError
    ValueError, TypeError, etc.

EXCEPTIONS — CHAINING:
  raise NewError("msg") from original_error
  raise NewError("msg") from None    # suppress context

CUSTOM EXCEPTIONS:
  class MyError(Exception):
      def __init__(self, detail):
          self.detail = detail
          super().__init__(f"My error: {detail}")

EXCEPTION GROUPS (Python 3.11+):
  raise ExceptionGroup("msg", [err1, err2])
  except* ValueError as eg:
      for e in eg.exceptions: ...

WARNINGS:
  import warnings
  warnings.warn("msg", DeprecationWarning)
  warnings.filterwarnings("ignore", category=...)

GOLDEN RULES:
  1. Catch specific exceptions, not bare except
  2. Never catch BaseException
  3. Order: specific → general
  4. Use custom exceptions for domain errors
  5. Chain exceptions with "from" for context
```
