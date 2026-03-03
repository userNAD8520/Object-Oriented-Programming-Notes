# Object-Oriented Programming in Python & UML Class Diagrams
### A Complete Beginner-to-Professional Lesson

---

## Table of Contents

1. [What Is Object-Oriented Programming?](#1-what-is-object-oriented-programming)
2. [Classes and Objects](#2-classes-and-objects)
3. [The `self` Parameter](#3-the-self-parameter)
4. [Instance Methods, Class Methods, and Static Methods](#4-instance-methods-class-methods-and-static-methods)
5. [Dunder (Magic) Methods — `__str__`, `__repr__`, and More](#5-dunder-magic-methods)
6. [Encapsulation — Protecting Your Data](#6-encapsulation)
7. [The `@property` Decorator](#7-the-property-decorator)
8. [Abstraction — Hiding Complexity](#8-abstraction)
9. [Object Relationships — Composition and Aggregation](#9-object-relationships)
10. [Inheritance — The "Is-A" Relationship](#10-inheritance)
11. [Polymorphism — Many Forms, One Interface](#11-polymorphism)
12. [Abstract Base Classes (ABCs)](#12-abstract-base-classes-abcs)
13. [UML Class Diagrams for Python Developers](#13-uml-class-diagrams-for-python-developers)
14. [UML Relationships in Depth](#14-uml-relationships-in-depth)
15. [Complete Capstone — Banking System (Code + UML)](#15-complete-capstone)

---

---

# 1. What Is Object-Oriented Programming?

---

## 1.1 Concept Overview

### Definition

**Object-Oriented Programming (OOP)** is a way of organizing code around "objects" — self-contained bundles of **data** (what the object *knows*) and **behavior** (what the object *can do*). Instead of writing a flat list of instructions, you model the world as interacting objects, just like real life.

### Why It Exists

Before OOP, developers wrote **procedural code**: a sequence of instructions executed top-to-bottom, with data stored in separate variables and functions. This works fine for small programs, but creates serious problems as software grows:

**Without OOP (Procedural approach — imagine tracking students):**
```python
# Data and logic are completely disconnected — a maintenance nightmare
student_names     = ["Alice", "Bob"]
student_ids       = ["A001", "A002"]
student_courses   = [[], []]

def enroll_student(index, course):
    student_courses[index].append(course)

# What happens when we need 1000 students?
# What if name and courses get out of sync?
# There's no protection — anyone can do: student_names[0] = None
```

**The problems this causes:**
- Data and the functions that work on it are scattered across the file
- Nothing prevents you from accidentally corrupting data
- Adding features means touching code in many unrelated places
- Reusing logic for a second project is nearly impossible

**With OOP, each student is a self-contained unit:**
```python
class Student:
    def __init__(self, name, student_id):
        self.name = name
        self.student_id = student_id
        self.courses = []

    def enroll(self, course):
        self.courses.append(course)
```

Now the data and its behavior live together, are protected, and are reusable.

### Mental Model (Intuition First)

> Think of a **cookie cutter** and **cookies**.
>
> The **class** is the cookie cutter — a template that defines the *shape* of every cookie (what data it has, what it can do).
> An **object** is an individual cookie — made from the cutter, but with its own specific frosting color, size, and toppings.

You can make thousands of cookies from one cutter. Each cookie is independent, but all share the same structure.

### The Five Pillars of OOP

| Pillar | Plain English |
|---|---|
| **Modelization** | Describe the real world in code — pick only the attributes relevant to your problem |
| **Encapsulation** | Bundle data + behavior together; hide internal details from the outside |
| **Abstraction** | Provide a simple interface; hide the complex machinery underneath |
| **Inheritance** | A new class can *extend* an existing one, reusing and adding to its behavior |
| **Polymorphism** | Different objects can respond to the same message in their own unique way |

---

---

# 2. Classes and Objects

---

## 2.1 Concept Overview

### Definition

- A **class** is a blueprint — it describes what data an object will hold (attributes) and what it can do (methods).
- An **object** (also called an **instance**) is a specific thing created from that blueprint. It has its own copy of the data.

### Mental Model

> A **class** is the architectural blueprint for a house.
> An **object** is an actual house built from that blueprint.
>
> Many houses can be built from the same blueprint, each with a different address, paint color, and owner — but all sharing the same structure.

### Visual Explanation

```
CLASS (Blueprint — exists once in memory)
┌─────────────────────────────┐
│  Student                    │
│  ───────────────────────    │
│  Attributes: name,          │
│              student_id,    │
│              courses        │
│  Methods:    display()      │
│              enroll()       │
└─────────────────────────────┘
         │              │
         │ create        │ create
         ▼              ▼
OBJECT 1                OBJECT 2
┌──────────────┐       ┌──────────────┐
│ name="Alice" │       │ name="Bob"   │
│ id="A001"    │       │ id="A002"    │
│ courses=[]   │       │ courses=[]   │
└──────────────┘       └──────────────┘
Each object has its OWN independent copy of the data.
```

## 2.2 Syntax & Structure

### Canonical Syntax

```python
class ClassName:                         # 1. Define the class
    def __init__(self, param1, param2):  # 2. The initializer method
        self.attribute1 = param1         # 3. Assign instance attributes
        self.attribute2 = param2

    def some_method(self):               # 4. Define behavior
        print(self.attribute1)

# Create objects (instances)
obj1 = ClassName("value1", "value2")     # 5. Instantiate
obj1.some_method()                       # 6. Call a method
```

### Line-by-Line Breakdown

| Line | What it does | Why it exists |
|---|---|---|
| `class ClassName:` | Declares a new class. `ClassName` follows PascalCase by convention. | Tells Python "I'm defining a blueprint called ClassName" |
| `def __init__(self, ...)` | The **initializer** — runs automatically every time a new object is created | Sets up the object's starting state; gives every new instance its own data |
| `self` | A reference to the specific object being created or used | Lets Python know *which* object's data to use; Python passes this automatically |
| `self.attribute = value` | Creates an **instance attribute** stored on that specific object | This is the object's personal data — unique to each instance |
| `def some_method(self)` | Defines a function that belongs to the class (a **method**) | Bundles behavior with data; all instances can call it |
| `obj1 = ClassName(...)` | Creates a new object — **instantiation** | Runs `__init__` and returns a live object stored in `obj1` |

> ⚠️ **Beginner Trap:** `__init__` is NOT the constructor — it's the *initializer*. Python creates the object first, then calls `__init__` to fill it with data. You never call `__init__` yourself.

## 2.3 Example 1 — Foundational

### Goal
Create the smallest meaningful class and two independent objects from it.

```python
# Define the blueprint
class Student:
    # __init__ is called automatically when we write Student("Alice", "A001")
    def __init__(self, name, student_number):
        # 'self.name' stores the name ON this specific object
        self.name = name
        # 'self.student_number' stores the ID ON this specific object
        self.student_number = student_number
        # Every new student starts with an empty course list
        self.courses = []

    # A method — a function attached to the class
    def display(self):
        # self.name refers to THIS object's name
        print(f"Name: {self.name}, ID: {self.student_number}")

    def enroll(self, course_name):
        self.courses.append(course_name)
        print(f"{self.name} has enrolled in {course_name}.")

# --- Instantiation ---
john = Student("John Doe", "A01234567")   # Creates object 1
bob  = Student("Bobby",    "A4432890")    # Creates object 2

# Call methods
john.display()
john.enroll("ACIT 2515")
bob.display()
```

### Step-by-Step Runtime Walkthrough

```
Step 1: Python reads the class definition and stores the blueprint in memory.
        No objects exist yet.

Step 2: john = Student("John Doe", "A01234567")
        → Python allocates memory for a new Student object
        → __init__ is called with self=<new object>, name="John Doe", student_number="A01234567"
        → self.name = "John Doe"         ← stored on john's object
        → self.student_number = "A01234567"
        → self.courses = []
        → john now points to this object in memory

        john ──► [ name="John Doe" | student_number="A01234567" | courses=[] ]

Step 3: bob = Student("Bobby", "A4432890")
        → A SEPARATE object is created in memory
        → bob's data is completely independent from john's

        bob  ──► [ name="Bobby" | student_number="A4432890" | courses=[] ]

Step 4: john.display()
        → Python automatically passes john as 'self'
        → Prints john's name and ID

Step 5: john.enroll("ACIT 2515")
        → john.courses becomes ["ACIT 2515"]
        → bob.courses is still []  (completely unaffected)
```

### Output

```
Name: John Doe, ID: A01234567
John Doe has enrolled in ACIT 2515.
Name: Bobby, ID: A4432890
```

## 2.4 Example 2 — Real-World / Interview Level

### Goal
Show a realistic class that uses a class attribute (shared across all instances) alongside instance attributes.

```python
class Student:
    # CLASS ATTRIBUTE — shared by ALL instances, lives on the class itself
    school_name = "BCIT"
    total_students = 0

    def __init__(self, name, student_number):
        self.name = name
        self.student_number = student_number
        self.courses = []
        # Modify the class attribute — note: we use Student.total_students,
        # NOT self.total_students, to explicitly update the class-level value
        Student.total_students += 1

    def display(self):
        # self.school_name works too — Python looks up class attrs if not on instance
        print(f"[{Student.school_name}] {self.name} (ID: {self.student_number})")

# Create two students
s1 = Student("Alice", "A001")
s2 = Student("Bob",   "A002")

s1.display()
s2.display()
print(f"Total students enrolled: {Student.total_students}")
```

### Deep Explanation

```
CLASS ATTRIBUTE vs INSTANCE ATTRIBUTE

Class Attribute (school_name, total_students):
  Student ──► [ school_name="BCIT" | total_students=2 ]
                    ▲                       ▲
                    │                       │
  s1 can read it    │     s2 can read it    │
  (but doesn't own it)    (but doesn't own it)

Instance Attribute (name, courses):
  s1 ──► [ name="Alice" | courses=[] ]   ← Alice's own data
  s2 ──► [ name="Bob"   | courses=[] ]   ← Bob's own data
```

**Why `Student.total_students += 1` instead of `self.total_students += 1`?**
If you write `self.total_students += 1`, Python creates a *new instance attribute* called `total_students` on that one object — it no longer modifies the shared class attribute. This is a very common bug.

### Output

```
[BCIT] Alice (ID: A001)
[BCIT] Bob (ID: A002)
Total students enrolled: 2
```

---

---

# 3. The `self` Parameter

---

## 3.1 Concept Overview

### Definition

`self` is a reference to the **current object** — the specific instance that a method is being called on. It is always the first parameter of any instance method.

### Why It Exists

Python needs to know *which* object's data to use when a method runs. When you call `john.display()`, Python translates this behind the scenes to `Student.display(john)`. The `self` parameter *is* `john` (or whichever object called the method).

### Mental Model

> Think of `self` as the word *"my"* in English.
>
> When a Student says `"My name is Alice"`, the word *"my"* refers to that specific student.
> `self.name` means *"my name"* — the name belonging to whichever student is speaking.

### Visual Explanation

```
john.display()

Python automatically rewrites this as:
Student.display(john)
                ↑
                self = john  ← "self" is just the object that called the method

Inside display():
  def display(self):
      print(f"Name: {self.name}")
                        ↑
                        self IS john, so this reads john.name = "John Doe"
```

> ⚠️ **The name `self` is a convention, not a keyword.** You could write `def display(this):` and it would work. But *always* use `self` — every Python developer expects it.

---

---

# 4. Instance Methods, Class Methods, and Static Methods

---

## 4.1 Concept Overview

### Definition

Python classes support three kinds of methods, each with different levels of access to the object and class data:

| Method Type | Decorator | First Parameter | Has Access To |
|---|---|---|---|
| **Instance Method** | *(none)* | `self` | Instance data + class data |
| **Class Method** | `@classmethod` | `cls` | Class data only |
| **Static Method** | `@staticmethod` | *(none)* | Neither — it's a utility function |

### Why Each Exists

- **Instance methods** are the default: they operate on a specific object's data.
- **Class methods** operate on the class itself — perfect for alternative constructors (creating objects in different ways) or managing class-wide counters.
- **Static methods** are helper/utility functions logically grouped inside the class but needing neither the instance nor the class.

### Mental Model

> Imagine a **bakery class**:
> - An **instance method** is like a baker working on *this specific cake* (self): "frost *my* cake."
> - A **class method** is like the bakery manager working on *the bakery as a whole* (cls): "how many cakes have we made in total?"
> - A **static method** is a recipe conversion utility: "convert grams to ounces" — it doesn't care which cake or which bakery; it's just math.

## 4.2 Syntax & Structure

```python
class MyClass:
    class_attribute = "shared"

    # Instance method — receives self (the object)
    def instance_method(self):
        return f"I can access instance and class data: {self.class_attribute}"

    # Class method — receives cls (the class itself)
    @classmethod
    def class_method(cls):
        return f"I can access class data: {cls.class_attribute}"

    # Static method — receives nothing
    @staticmethod
    def static_method(x, y):
        return x + y  # Pure utility — no self, no cls
```

## 4.3 Example 1 — Class Method as Alternative Constructor

### Goal
Show the most common real-world use of `@classmethod`: creating an object from different input formats.

```python
class Date:
    # Standard constructor — expects three separate integers
    def __init__(self, year, month, day):
        self.year  = year
        self.month = month
        self.day   = day

    # Alternative constructor — builds a Date from a string like "2024-03-15"
    @classmethod
    def from_string(cls, date_string):
        # date_string = "2024-03-15"
        # split('-') gives ["2024", "03", "15"]
        # map(int, ...) converts each to an integer: [2024, 3, 15]
        year, month, day = map(int, date_string.split('-'))
        # cls is the Date class itself — so cls(year, month, day)
        # is the same as Date(year, month, day)
        return cls(year, month, day)

    def __str__(self):
        # :02d means "at least 2 digits, pad with zero if needed"
        return f"{self.year}-{self.month:02d}-{self.day:02d}"

# Using the standard constructor
date1 = Date(2024, 3, 15)
print(date1)   # 2024-03-15

# Using the class method constructor — accepts a string instead
date2 = Date.from_string("2024-03-15")
print(date2)   # 2024-03-15
```

### Step-by-Step Runtime Walkthrough

```
Date.from_string("2024-03-15")

Step 1: Python calls from_string with cls=Date, date_string="2024-03-15"
Step 2: "2024-03-15".split('-')  →  ["2024", "03", "15"]
Step 3: map(int, ...)            →  [2024, 3, 15]
Step 4: year=2024, month=3, day=15
Step 5: cls(2024, 3, 15)         →  Date(2024, 3, 15)  →  new Date object
Step 6: The new Date object is returned and stored in date2
```

### Output

```
2024-03-15
2024-03-15
```

## 4.4 Example 2 — Static Method for Validation

### Goal
Show how `@staticmethod` groups utility logic with its class without needing instance or class state.

```python
class User:
    def __init__(self, username, email):
        # Validate BEFORE storing — use the static method
        if not User.is_valid_email(email):
            # Raise an error if the email is invalid — object creation is blocked
            raise ValueError(f"Invalid email address: {email}")
        if not User.is_valid_username(username):
            raise ValueError(f"Invalid username: {username}")

        self.username = username
        self.email    = email

    @staticmethod
    def is_valid_email(email):
        """Returns True if email contains '@' and a '.' after '@'."""
        # Check for '@' and that the domain part contains a '.'
        return '@' in email and '.' in email.split('@')[1]

    @staticmethod
    def is_valid_username(username):
        """Returns True if username is at least 3 chars and alphanumeric."""
        return len(username) >= 3 and username.isalnum()

# Valid user — succeeds
user = User("alice99", "alice@example.com")
print(f"User created: {user.username}")

# Invalid email — raises ValueError at creation time
try:
    bad_user = User("bob", "not-an-email")
except ValueError as e:
    print(f"Error: {e}")
```

### Deep Explanation

**Why static methods for validation?**
- The validation logic (`is_valid_email`) doesn't need any instance data — it just checks a string.
- By making it a static method on `User`, we keep related logic together (no floating utility functions in the module).
- We can also call `User.is_valid_email("test@test.com")` *before* creating an object — useful for form validation in web applications.

**Runtime Flow:**
```
User("alice99", "alice@example.com")
         │
         ▼
   __init__ is called
         │
         ▼
   User.is_valid_email("alice@example.com")
         │── '@' found? YES
         │── '.' in 'example.com'? YES
         │── returns True
         │
         ▼
   User.is_valid_username("alice99")
         │── len >= 3? YES
         │── isalnum()? YES
         │── returns True
         │
         ▼
   self.username = "alice99"
   self.email    = "alice@example.com"
   Object created successfully ✓
```

### Output

```
User created: alice99
Error: Invalid email address: not-an-email
```

---

---

# 5. Dunder (Magic) Methods

---

## 5.1 Concept Overview

### Definition

**Dunder methods** (short for **Double UNDERscore** methods) are special methods with names surrounded by double underscores: `__init__`, `__str__`, `__repr__`, `__add__`, etc. Python calls them *automatically* in response to specific operations — you never call them directly.

### Why They Exist

They let your custom objects behave like Python's built-in types. Without them:

```python
student = Student("Alice", "A001")
print(student)
# Output: <__main__.Student object at 0x7f3c1a2b4e50>
# Completely useless!
```

With `__str__`:
```python
print(student)
# Output: Student: Alice (A001)
```

### Mental Model

> Dunder methods are Python's **automatic receptionist**.
>
> When you call `print(obj)`, Python walks up to the receptionist and says "someone wants to print this object." The receptionist checks if the object has a `__str__` method. If yes, it calls it automatically. You don't need to call it yourself — Python handles the routing.

### The Two Most Important: `__str__` vs `__repr__`

| | `__str__` | `__repr__` |
|---|---|---|
| **Purpose** | Human-friendly display | Developer-friendly debugging |
| **Audience** | End users | You (the developer) |
| **Called by** | `print()`, `str()`, f-strings | `repr()`, interactive Python shell, lists |
| **Goal** | Readable and clean | Unambiguous; ideally recreates the object |
| **If missing** | Falls back to `__repr__` | Shows `<ClassName object at 0x...>` |

> **Rule of Thumb:** Always define `__repr__`. Define `__str__` when user-facing output needs to differ.

## 5.2 Syntax & Structure

```python
class ClassName:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __str__(self):
        # Must return a STRING — for human display
        return f"({self.x}, {self.y})"

    def __repr__(self):
        # Must return a STRING — for developer/debugging display
        return f"ClassName(x={self.x}, y={self.y})"
```

## 5.3 Example 1 — Foundational: `__str__` and `__repr__`

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        # Developer-friendly: looks like valid Python to recreate the object
        return f"Point(x={self.x}, y={self.y})"

    def __str__(self):
        # User-friendly: clean and readable
        return f"({self.x}, {self.y})"

p = Point(3, 4)

# __str__ is called by print() and in f-strings
print(p)                       # (3, 4)
print(f"The point is: {p}")    # The point is: (3, 4)

# __repr__ is called by repr() and when objects appear inside containers
print(repr(p))                 # Point(x=3, y=4)

points = [Point(1, 2), Point(3, 4)]
print(points)                  # [Point(x=1, y=2), Point(x=3, y=4)]
# Notice: inside a list, Python uses __repr__ for each element
```

### Step-by-Step Runtime Walkthrough

```
print(p)
  → Python calls p.__str__()
  → Returns "(3, 4)"
  → print() outputs: (3, 4)

repr(p)
  → Python calls p.__repr__()
  → Returns "Point(x=3, y=4)"

print(points)
  → Python calls __repr__ on each element inside the list
  → [Point(x=1, y=2), Point(x=3, y=4)]
```

### Output

```
(3, 4)
The point is: (3, 4)
Point(x=3, y=4)
[Point(x=1, y=2), Point(x=3, y=4)]
```

## 5.4 Example 2 — Real-World: Operator Overloading with Dunder Methods

### Goal
Show how dunder methods let you use `+`, `==`, and `<` with custom objects — just like Python's built-in numbers do.

```python
class Money:
    def __init__(self, amount, currency="USD"):
        self.amount   = amount    # e.g., 19.99
        self.currency = currency  # e.g., "USD"

    def __str__(self):
        # User-facing: clean format
        return f"${self.amount:.2f} {self.currency}"

    def __repr__(self):
        # Developer-facing: recreatable
        return f"Money({self.amount}, '{self.currency}')"

    def __eq__(self, other):
        # Called when you write: price1 == price2
        # Must return True or False
        return self.amount == other.amount and self.currency == other.currency

    def __lt__(self, other):
        # Called when you write: price1 < price2
        if self.currency != other.currency:
            raise ValueError("Cannot compare different currencies")
        return self.amount < other.amount

    def __add__(self, other):
        # Called when you write: price1 + price2
        # Must return a NEW Money object (don't modify self)
        if self.currency != other.currency:
            raise ValueError("Cannot add different currencies")
        return Money(self.amount + other.amount, self.currency)

# Create two prices
price1 = Money(19.99)
price2 = Money(29.99)

print(price1)                   # $19.99 USD         (calls __str__)
print(price1 == price2)         # False               (calls __eq__)
print(price1 < price2)          # True                (calls __lt__)

total = price1 + price2         # Calls __add__, returns new Money object
print(total)                    # $49.98 USD
```

### Deep Explanation

**Why is `__add__` creating a new `Money` object instead of modifying `self`?**
Python's built-in numbers are *immutable* — `3 + 4` doesn't change `3`, it creates `7`. We follow the same pattern: `price1 + price2` creates a brand-new `Money` object. This prevents unexpected side effects.

**Runtime Flow:**
```
price1 + price2
     │
     ▼
Python calls price1.__add__(price2)
     │── currencies match? YES
     │── 19.99 + 29.99 = 49.98
     │── return Money(49.98, "USD")  ← new object
     ▼
total = Money(49.98, "USD")
print(total)  →  calls total.__str__()  →  "$49.98 USD"
```

### Output

```
$19.99 USD
False
True
$49.98 USD
```

---

---

# 6. Encapsulation

---

## 6.1 Concept Overview

### Definition

**Encapsulation** is the practice of:
1. **Bundling** data (attributes) and the methods that work on it into one class
2. **Controlling access** to that data — preventing external code from directly reading or modifying internal state

### Why It Exists

Without encapsulation, any code anywhere in your program can reach into your object and break its data:

```python
# Without encapsulation — dangerous!
my_account._balance = -999999  # Instant corruption, no validation
```

With encapsulation, all changes *must* go through controlled methods:
```python
my_account.withdraw(50)  # Validates, checks funds, then modifies safely
```

### Python's Privacy Model

Python does **not** have true private members like Java or C++. Instead, it uses a naming convention:

| Convention | Syntax | Meaning |
|---|---|---|
| **Public** | `self.name` | Anyone can read/write |
| **Protected** | `self._name` | "Please don't touch this directly" (internal use) |
| **Private (name mangled)** | `self.__name` | Python renames to `self._ClassName__name` — harder to access |

> ⚠️ Python trusts developers. Single underscore (`_`) is the standard way to say "this is internal." Double underscore (`__`) is used rarely — mainly to avoid naming conflicts in complex inheritance.

## 6.2 Example 1 — Foundational: Protected Attribute

```python
class BankAccount:
    def __init__(self, account_holder, initial_balance=0):
        self.account_holder = account_holder
        # _balance: single underscore = "protected" — do not modify directly
        self._balance = initial_balance

    def deposit(self, amount):
        # Validation: only allow positive deposits
        if amount > 0:
            self._balance += amount
            print(f"Deposited ${amount}. New balance: ${self._balance}")
        else:
            print("Deposit amount must be positive.")

    def withdraw(self, amount):
        # Validation: can't withdraw more than available
        if 0 < amount <= self._balance:
            self._balance -= amount
            print(f"Withdrew ${amount}. New balance: ${self._balance}")
        else:
            print("Invalid amount or insufficient funds.")

    def get_balance(self):
        # A "getter" — safe read-only access to the balance
        return self._balance

# Usage
account = BankAccount("John Doe", 100)
account.deposit(50)
account.withdraw(200)   # Blocked — insufficient funds
account.withdraw(30)
print(f"Final balance: ${account.get_balance()}")
```

### Step-by-Step Runtime Walkthrough

```
account = BankAccount("John Doe", 100)
  → account.account_holder = "John Doe"
  → account._balance = 100              ← protected attribute

account.deposit(50)
  → amount=50 > 0? YES
  → _balance = 100 + 50 = 150
  → prints: "Deposited $50. New balance: $150"

account.withdraw(200)
  → 0 < 200 <= 150? NO (200 > 150)
  → prints: "Invalid amount or insufficient funds."

account.withdraw(30)
  → 0 < 30 <= 150? YES
  → _balance = 150 - 30 = 120
```

### Output

```
Deposited $50. New balance: $150
Invalid amount or insufficient funds.
Withdrew $30. New balance: $120
Final balance: $120
```

---

---

# 7. The `@property` Decorator

---

## 7.1 Concept Overview

### Definition

The `@property` decorator turns a method into a **virtual attribute** — it looks like a plain attribute from the outside, but secretly runs a method when accessed. Combined with `.setter`, you get controlled read/write access.

### Why It Exists

Without `@property`, you're forced to choose between:
- `obj.balance` — simple, but exposes raw data with no protection
- `obj.get_balance()` — safe, but verbose and "un-Pythonic"

`@property` gives you both: the clean attribute syntax *and* the safety of a method.

### Visual Explanation

```
WITHOUT @property:
  my_account.get_balance()    ← method call syntax, verbose

WITH @property:
  my_account.balance          ← attribute syntax, clean
                              BUT actually calls balance() method internally!

Outside world sees:           What Python actually does:
  my_account.balance    ──►   my_account.balance()
  (looks like data)           (runs code secretly)
```

## 7.2 Syntax & Structure

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius      # Store data with underscore (protected)

    @property
    def radius(self):
        """Getter — called when you READ: obj.radius"""
        return self._radius

    @radius.setter
    def radius(self, value):
        """Setter — called when you WRITE: obj.radius = value"""
        if value < 0:
            raise ValueError("Radius cannot be negative")
        self._radius = value

    @property
    def area(self):
        """Computed property — no setter (read-only)"""
        import math
        return math.pi * self._radius ** 2
```

### Line-by-Line Breakdown

- `@property` on `radius(self)`: defines the **getter**. Called whenever someone reads `circle.radius`.
- `@radius.setter` on `radius(self, value)`: defines the **setter**. Called whenever someone writes `circle.radius = 5`.
- `area` has only a getter — it's **read-only**. Trying to set `circle.area = 10` raises `AttributeError`.

## 7.3 Example 1 — Foundational

```python
class BankAccount:
    def __init__(self, holder, balance=0):
        self.holder   = holder
        self._balance = balance   # The real data, protected

    @property
    def balance(self):
        """Getter — read the balance cleanly"""
        return self._balance

    def deposit(self, amount):
        if amount > 0:
            self._balance += amount

# Usage
account = BankAccount("Alice", 500)

# Read: triggers @property getter — looks like an attribute, runs a method
print(account.balance)   # 500

account.deposit(100)
print(account.balance)   # 600

# This would raise AttributeError — no setter defined:
# account.balance = 9999
```

### Output

```
500
600
```

## 7.4 Example 2 — Real-World: Setter with Validation

```python
class TV:
    def __init__(self):
        self._is_on  = False
        self._channel = 1
        self._volume  = 10

    @property
    def channel(self):
        return self._channel

    @channel.setter
    def channel(self, new_channel):
        # Setter contains all the validation logic
        if not self._is_on:
            print("Cannot change channel — TV is off.")
        elif new_channel > 0:
            self._channel = new_channel
            print(f"Channel changed to {self._channel}.")
        else:
            print("Invalid channel number.")

    @property
    def volume(self):
        return self._volume

    @volume.setter
    def volume(self, new_volume):
        if not self._is_on:
            print("Cannot change volume — TV is off.")
        elif 0 <= new_volume <= 100:
            self._volume = new_volume
            print(f"Volume set to {self._volume}.")
        else:
            print("Volume must be between 0 and 100.")

    def power(self):
        self._is_on = not self._is_on
        print(f"TV is now {'On' if self._is_on else 'Off'}.")

# Usage
tv = TV()
tv.power()        # Turn on
tv.channel = 5    # Setter runs, validates, sets _channel = 5
tv.volume  = 20   # Setter runs, validates, sets _volume = 20
print(f"Channel: {tv.channel}, Volume: {tv.volume}")

tv.power()        # Turn off
tv.channel = 10   # Setter blocks it: "Cannot change channel — TV is off."
```

### Output

```
TV is now On.
Channel changed to 5.
Volume set to 20.
Channel: 5, Volume: 20
TV is now Off.
Cannot change channel — TV is off.
```

---

---

# 8. Abstraction

---

## 8.1 Concept Overview

### Definition

**Abstraction** means hiding complex internal logic and exposing only what the user needs. The user of your object sees a simple, clean interface — they don't need to know *how* it works, only *what* it does.

### Mental Model

> Think of driving a car. You use a steering wheel, accelerator, and brakes. You don't need to understand fuel injection, combustion, or differential gears. The car's complex internals are **abstracted away** behind a simple interface.

### Abstraction vs Encapsulation

These are closely related but distinct:

| | Encapsulation | Abstraction |
|---|---|---|
| **Focus** | Protecting and bundling data | Hiding complexity, showing essentials |
| **How** | Access modifiers, properties | Simple public interfaces, hiding internals |
| **Question it answers** | "Who can touch this?" | "What does this do (not how)?" |

---

---

# 9. Object Relationships

---

## 9.1 Concept Overview

Objects rarely exist alone. They relate to each other in structured ways. The two most important "has-a" relationships are **Composition** and **Aggregation**.

### Visual: Composition vs Aggregation

```
COMPOSITION — "Part cannot exist without the whole"

  Order ──────────────────────► LineItem
  (whole)         owns         (part)
                               Created INSIDE Order
                               Destroyed WITH Order

  del order  →  LineItems also destroyed ✓

─────────────────────────────────────────────────

AGGREGATION — "Part can exist independently"

  Department ─ ─ ─ ─ ─ ─ ─ ─ ► Professor
  (container)      references  (independent object)
                               Created OUTSIDE Department
                               Survives if Department is deleted

  del department  →  Professors still exist ✓
```

## 9.2 Example: Composition

```python
class LineItem:
    # LineItem has no meaning outside of an Order
    def __init__(self, product, quantity, price):
        self.product  = product
        self.quantity = quantity
        self.price    = price

    def subtotal(self):
        return self.quantity * self.price

class Order:
    def __init__(self, order_id):
        self.order_id = order_id
        self._items = []   # Owns the LineItems — creates them internally

    def add_item(self, product, quantity, price):
        # LineItem is created INSIDE Order — it belongs to this order
        item = LineItem(product, quantity, price)
        self._items.append(item)

    def total(self):
        # sum() over a generator expression — adds each subtotal
        return sum(item.subtotal() for item in self._items)

# Usage
order = Order("ORD-001")
order.add_item("Laptop", 1, 999.99)
order.add_item("Mouse",  2, 29.99)
print(f"Order total: ${order.total():.2f}")
```

### Output

```
Order total: $1059.97
```

## 9.3 Example: Aggregation

```python
class Professor:
    # Professor exists independently — NOT created by Department
    def __init__(self, name):
        self.name = name

class Department:
    def __init__(self, name):
        self.name = name
        self._professors = []   # Holds references — does NOT own them

    def add_professor(self, professor):
        # Professor is passed IN — it was created externally
        self._professors.append(professor)
        print(f"{professor.name} joined {self.name}.")

# Professors created INDEPENDENTLY
prof_smith = Professor("Dr. Smith")
prof_jones = Professor("Dr. Jones")

comp_sci = Department("Computer Science")
comp_sci.add_professor(prof_smith)
comp_sci.add_professor(prof_jones)

# If Department is deleted, prof_smith and prof_jones still exist
del comp_sci
print(f"{prof_smith.name} still exists!")  # Dr. Smith still exists!
```

### Output

```
Dr. Smith joined Computer Science.
Dr. Jones joined Computer Science.
Dr. Smith still exists!
```

---

---

# 10. Inheritance

---

## 10.1 Concept Overview

### Definition

**Inheritance** models an **"is-a" relationship** — a subclass (child class) *is a* more specific version of the parent class. The child inherits all the parent's attributes and methods and can add new ones or override existing ones.

### Why It Exists

Without inheritance, you'd copy-paste shared code across every class — a maintenance nightmare. Change a bug in one copy, and you have to find all the others.

### Mental Model

> A **Dog** IS AN **Animal**. A **Manager** IS AN **Employee**.
>
> The child class gets everything the parent has for free — and adds its own specialization on top.

### Visual Explanation

```
           Employee
           ├── name
           ├── employee_id
           └── display()         ← inherited by BOTH children
                │
      ┌─────────┴──────────┐
      ▼                    ▼
   Manager             Salesperson
   └── team             └── commission_rate
   (adds new attr)      (adds new attr)
   (inherits display()) (inherits display())
```

## 10.2 Syntax & Structure

```python
class Parent:
    def __init__(self, shared_attr):
        self.shared_attr = shared_attr

    def shared_method(self):
        return self.shared_attr

class Child(Parent):             # Child INHERITS from Parent
    def __init__(self, shared_attr, child_attr):
        super().__init__(shared_attr)  # Call parent's __init__ first!
        self.child_attr = child_attr   # Then add child-specific data

    def child_method(self):
        return self.child_attr
```

> ⚠️ **Critical Rule:** Always call `super().__init__(...)` in the child's `__init__`. If you skip it, the parent's attributes are never created — your object is broken.

## 10.3 Example 1 — Foundational

```python
class Employee:
    def __init__(self, name, employee_id):
        self.name        = name
        self.employee_id = employee_id

    def display(self):
        return f"ID: {self.employee_id}, Name: {self.name}"

# Manager IS AN Employee — inherits name, employee_id, and display()
class Manager(Employee):
    def __init__(self, name, employee_id, team):
        # Step 1: Run the parent's __init__ to set name and employee_id
        super().__init__(name, employee_id)
        # Step 2: Add Manager-specific data
        self.team = team

# Salesperson IS AN Employee
class Salesperson(Employee):
    def __init__(self, name, employee_id, commission_rate):
        super().__init__(name, employee_id)
        self.commission_rate = commission_rate

manager    = Manager("Jane Doe",   "M01", ["Sales", "Support"])
salesperson = Salesperson("John Smith", "S01", 0.05)

# Both use the INHERITED display() method from Employee
print(manager.display())
print(salesperson.display())
# Manager-specific attribute
print(f"Manager's team: {manager.team}")
```

### Step-by-Step Runtime Walkthrough

```
Manager("Jane Doe", "M01", ["Sales", "Support"])
  → Manager.__init__ runs
  → super().__init__("Jane Doe", "M01")
      → Employee.__init__ runs
      → self.name = "Jane Doe"
      → self.employee_id = "M01"
  → self.team = ["Sales", "Support"]

manager.display()
  → Python looks for display() on Manager — not found
  → Python looks up the inheritance chain to Employee — found!
  → Calls Employee.display(manager)
  → Returns "ID: M01, Name: Jane Doe"
```

### Output

```
ID: M01, Name: Jane Doe
ID: S01, Name: John Smith
Manager's team: ['Sales', 'Support']
```

## 10.4 Example 2 — Method Overriding

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        # Default behavior — meant to be overridden
        return f"{self.name} makes a sound."

class Dog(Animal):
    def speak(self):
        # OVERRIDE — replace the parent's speak() with Dog-specific behavior
        return f"{self.name} says: Woof!"

class Cat(Animal):
    def speak(self):
        return f"{self.name} says: Meow!"

animals = [Dog("Rex"), Cat("Whiskers"), Dog("Buddy")]

for animal in animals:
    # Python calls the CORRECT speak() based on the object's actual type
    print(animal.speak())
```

### Output

```
Rex says: Woof!
Whiskers says: Meow!
Buddy says: Woof!
```

---

---

# 11. Polymorphism

---

## 11.1 Concept Overview

### Definition

**Polymorphism** (Greek: "many forms") means that a single method name can behave differently depending on which object it is called on. You can write code that works with *any* object that supports a given interface — without caring about the specific type.

### Why It Exists

Without polymorphism, you'd write:
```python
if type(animal) == Dog:
    animal.bark()
elif type(animal) == Cat:
    animal.meow()
# ... and a new elif for EVERY new animal type
```

With polymorphism:
```python
animal.speak()  # Works for Dog, Cat, or any future animal
```

Adding a new animal type never requires changing existing code.

### Visual Explanation

```
shapes = [Circle(), Square(), Triangle()]

for shape in shapes:
    shape.draw()
    ↑
    Python looks at the ACTUAL type of shape
    and calls THAT type's draw() method

  Circle.draw()   → "Drawing a circle: O"
  Square.draw()   → "Drawing a square: []"
  Triangle.draw() → "Drawing a triangle: /\"
```

## 11.2 Example — Full Polymorphism Demo

```python
class Shape:
    # Base class — defines the interface
    def draw(self):
        # Raise an error if a subclass forgets to implement draw()
        raise NotImplementedError("Subclasses must implement draw()")

class Circle(Shape):
    def draw(self):
        print("Drawing a circle: O")

class Square(Shape):
    def draw(self):
        print("Drawing a square: []")

class Triangle(Shape):
    def draw(self):
        print("Drawing a triangle: /\\")

# A list of DIFFERENT types — but all share the same interface
shapes = [Circle(), Square(), Triangle(), Circle()]

for shape in shapes:
    shape.draw()   # Python automatically calls the right version
```

### Output

```
Drawing a circle: O
Drawing a square: []
Drawing a triangle: /\
Drawing a circle: O
```

---

---

# 12. Abstract Base Classes (ABCs)

---

## 12.1 Concept Overview

### Definition

An **Abstract Base Class (ABC)** is a class that:
1. **Cannot be instantiated** directly — you can never create an `Animal()` if `Animal` is abstract
2. **Forces subclasses** to implement specific methods — if they don't, Python raises an error

ABCs formalize and *enforce* polymorphism.

### Why They Exist

Without ABCs:
```python
class Storage:
    def save(self, data): pass
    def load(self, id):   pass

class FileStorage(Storage):
    def save(self, data): ...
    # Forgot to implement load() — but Python doesn't notice!
    # Bug discovered only at runtime when load() is called
```

With ABCs:
```python
# Python immediately raises TypeError when FileStorage is defined
# if it's missing any abstract method — catch errors at class definition time
```

### When To Use
- Defining a shared interface that multiple classes must follow
- Building plugin systems or swappable backends (storage, payment processors, etc.)
- Making your codebase's "contracts" explicit and enforced

### When NOT To Use
- Simple scripts or small projects
- When a regular class with reasonable default behavior is sufficient

## 12.2 Syntax & Structure

```python
from abc import ABC, abstractmethod

class AbstractClass(ABC):          # Inherit from ABC to make it abstract
    @abstractmethod
    def required_method(self):     # Subclasses MUST implement this
        pass                       # Body is irrelevant — usually just 'pass'

    def optional_method(self):     # Regular methods can also be in ABCs
        return "default behavior"
```

## 12.3 Example — Real-World: Storage Interface

```python
from abc import ABC, abstractmethod

# Abstract class — defines the contract
class Storage(ABC):
    @abstractmethod
    def save(self, data):
        """Every Storage type MUST implement this."""
        pass

    @abstractmethod
    def load(self, identifier):
        """Every Storage type MUST implement this."""
        pass

# Concrete class 1 — fulfills the contract
class FileStorage(Storage):
    def save(self, data):
        print(f"Saving to file: {data}")

    def load(self, identifier):
        print(f"Loading from file: {identifier}")
        return "file_data"

# Concrete class 2 — fulfills the contract
class DatabaseStorage(Storage):
    def save(self, data):
        print(f"Saving to database: {data}")

    def load(self, identifier):
        print(f"Loading from database ID: {identifier}")
        return "db_data"

# You CANNOT instantiate an abstract class:
# s = Storage()   ← TypeError: Can't instantiate abstract class Storage
#                   with abstract methods load, save

# The rest of your app works with the INTERFACE, not the specific class
def process(storage: Storage):
    storage.save("some important data")
    result = storage.load("record_001")
    print(f"Got: {result}")

process(FileStorage())
process(DatabaseStorage())
```

### Output

```
Saving to file: some important data
Loading from file: record_001
Got: file_data
Saving to database: some important data
Loading from database ID: record_001
Got: db_data
```

---

---

# 13. UML Class Diagrams for Python Developers

---

## 13.1 Concept Overview

### Definition

**UML (Unified Modeling Language)** class diagrams are standardized visual blueprints of your code's structure — showing classes, their attributes, methods, and how they relate to each other. Think of them as architectural drawings for software.

### Why UML Exists

| Problem | How UML Solves It |
|---|---|
| Complex code is hard to explain to teammates | A diagram communicates structure instantly |
| You build the wrong thing | Design on paper before writing code |
| Code rots as systems grow | Diagrams reveal structural problems early |
| Onboarding new developers is slow | A diagram gives instant system overview |

### Mental Model

> UML is like a **map of a city** before you build it. Architects draw blueprints first — they don't start pouring concrete and figure out where the walls go later. UML is your software blueprint.

## 13.2 Anatomy of a UML Class Box

A class is represented as a rectangle divided into three sections:

```
┌─────────────────────────────┐
│         ClassName           │  ← Section 1: Class Name (centered, bold)
├─────────────────────────────┤
│  + attribute1: type         │  ← Section 2: Attributes
│  - _attribute2: type        │     (visibility symbol + name + type)
│  # _attribute3: type        │
├─────────────────────────────┤
│  + method1(): return_type   │  ← Section 3: Methods
│  + method2(param: type): T  │     (visibility + name + params + return)
│  - _private_method(): None  │
└─────────────────────────────┘
```

## 13.3 Visibility Modifiers

| UML Symbol | Visibility | Python Convention |
|---|---|---|
| `+` | **Public** | `self.name` |
| `-` | **Private** | `self.__name` (name mangled) |
| `#` | **Protected** | `self._name` |
| `~` | **Package** | Not directly applicable in Python |

> ⚠️ **In most course settings, class diagrams show only the PUBLIC interface** (`+` members). Private implementation details are hidden — this mirrors the principle of abstraction.

## 13.4 Full Example: Car Class

### UML Diagram (text notation)

```
┌────────────────────────────────────────┐
│                  Car                   │
├────────────────────────────────────────┤
│  + brand: str                          │
│  + model: str                          │
│  - _mileage: float                     │
│  + is_electric: bool                   │
├────────────────────────────────────────┤
│  + mileage(): float                    │
│  + get_description(): str              │
│  + drive(): None                       │
│  + stop(distance: float): None         │
└────────────────────────────────────────┘
```

### Corresponding Python Code

```python
class Car:
    def __init__(self, brand: str, model: str, is_electric: bool):
        self.brand       = brand         # + public
        self.model       = model         # + public
        self._mileage    = 0.0           # - private (protected by convention)
        self.is_electric = is_electric   # + public

    def mileage(self) -> float:
        """Getter for mileage."""
        return self._mileage

    def get_description(self) -> str:
        fuel = "Electric" if self.is_electric else "Gas"
        return f"{self.brand} {self.model} ({fuel})"

    def drive(self) -> None:
        print(f"{self.brand} is driving...")

    def stop(self, distance: float) -> None:
        self._mileage += distance
        print(f"Stopped. Total mileage: {self._mileage:.1f} km")
```

## 13.5 Python-Specific UML Conventions

### Properties in UML

Because `@property` looks like an attribute but is a method, it's placed in the **methods section** with a `~property~` stereotype tag:

```
┌────────────────────────────────┐
│             Circle             │
├────────────────────────────────┤
│  - _radius: float              │
├────────────────────────────────┤
│  + radius: float  ~property~   │
│  + area: float    ~property~   │
│  + __init__(radius: float)     │
└────────────────────────────────┘
```

### Class Attributes and Static/Class Methods

```
┌────────────────────────────────┐
│           MathUtils            │
├────────────────────────────────┤
│  + PI: float  ~class attr~     │
├────────────────────────────────┤
│  + add(a, b): float  $         │  ← $ = static method
│  + from_string(s: str)*        │  ← * = abstract method
└────────────────────────────────┘
```

---

---

# 14. UML Relationships in Depth

---

## 14.1 Overview of Relationships

Objects are never isolated — they exist in a web of relationships. UML gives us precise notation for four key relationship types:

```
Relationship Types at a Glance:

Association   A ────── B       "A uses B"
Aggregation   A ──────◇ B      "A has B (B can exist alone)"
Composition   A ──────◆ B      "A owns B (B dies with A)"
Inheritance   A ──────▷ B      "A is a B"
```

## 14.2 Association

**A basic "uses" relationship** — one class references another, but neither owns the other.

### UML Notation
```
Student ────────── Course
```

### Python Code
```python
class Course:
    def __init__(self, name):
        self.name = name

class Student:
    def __init__(self, name):
        self.name    = name
        self.courses = []    # Student references Course objects

    def enroll(self, course):
        self.courses.append(course)

# Both objects exist independently; Student just keeps a reference
math   = Course("Math 101")
alice  = Student("Alice")
alice.enroll(math)
```

## 14.3 Aggregation ("Has-a" — Weak)

**The "whole-part" relationship where the part can exist independently.**

### UML Notation
```
Library ──────◇ Book
(hollow diamond on the "whole" side)
```

### Python Code
```python
class Book:
    def __init__(self, title):
        self.title = title
        # Book is created OUTSIDE Library — it's independent

class Library:
    def __init__(self):
        self.books = []

    def add_book(self, book):
        # Library receives a Book — it doesn't create it
        self.books.append(book)

# Book exists before and after the Library
great_gatsby = Book("The Great Gatsby")
library = Library()
library.add_book(great_gatsby)
del library
print(great_gatsby.title)  # Book still exists! → "The Great Gatsby"
```

## 14.4 Composition ("Part-of" — Strong)

**The part cannot exist without the whole — the whole creates and owns the part.**

### UML Notation
```
House ──────◆ Room
(filled diamond on the "whole" side)
```

### Python Code
```python
class Room:
    def __init__(self, name):
        self.name = name

class House:
    def __init__(self):
        # Rooms are created INSIDE House — they don't exist independently
        self.rooms = [Room("Living Room"), Room("Bedroom"), Room("Kitchen")]

    def list_rooms(self):
        for room in self.rooms:
            print(f"  - {room.name}")

house = House()
house.list_rooms()
del house  # All rooms are destroyed with the house
```

## 14.5 Inheritance ("Is-a")

**A subclass IS A specialization of the parent class.**

### UML Notation
```
       Animal
         ▲
    ┌────┴────┐
   Dog       Cat
(hollow arrowhead pointing TO the parent)
```

### Python Code
```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        raise NotImplementedError

class Dog(Animal):
    def speak(self):
        return f"{self.name} says: Woof!"

class Cat(Animal):
    def speak(self):
        return f"{self.name} says: Meow!"
```

## 14.6 Multiplicity

Multiplicity tells you **how many** of each class participates in a relationship.

### UML Notation

```
Person "1" ────────── "0..*" Car
  │                              │
  │                              └── "Zero or more cars"
  └── "Exactly one person"
```

### Common Multiplicity Symbols

| Notation | Meaning |
|---|---|
| `1` | Exactly one |
| `0..1` | Zero or one (optional) |
| `0..*` or `*` | Zero or more |
| `1..*` | One or more (at least one) |
| `2..5` | Between 2 and 5 |

### Aggregation vs Composition — Side-by-Side

```
AGGREGATION                          COMPOSITION
────────────────────────────────     ────────────────────────────────
Library ──◇── Book                   House ──◆── Room

◇ = hollow diamond                   ◆ = filled diamond
"has a" (weak)                       "part of" (strong)
Book exists independently            Room dies with House
Book passed INTO Library             Room created INSIDE House
```

## 14.7 Complete UML Example: Car System

```
┌────────────────────┐
│        Car         │
├────────────────────┤
│  - engine: Engine  │
│  - driver: Driver  │
├────────────────────┤
└────────────────────┘
      │          │
  ◆ (comp)   ◇ (aggr)
      │          │
      ▼          ▼
┌──────────┐  ┌──────────────┐
│  Engine  │  │    Driver    │
├──────────┤  ├──────────────┤
│+ power:  │  │+license: str │
│  int     │  ├──────────────┤
├──────────┤  │+pay_icbc():  │
│+ start() │  │  None        │
│+ stop()  │  └──────────────┘
└──────────┘
```

**Explanation:**
- `Car ◆── Engine` — **Composition**: the engine is created inside the car. No car = no engine.
- `Car ◇── Driver` — **Aggregation**: the driver exists independently. The car references the driver, but the driver can exist without the car (and drive another car tomorrow).

---

---

# 15. Complete Capstone

## Banking System — Code + UML

---

## 15.1 UML Diagram

```
┌────────────────────────────────┐
│        Account <<abstract>>    │
├────────────────────────────────┤
│  - _balance: float             │
│  + number: str                 │
│  + owner: str                  │
├────────────────────────────────┤
│  + deposit(amount: float): None│
│  + withdraw(amount: float)*    │  ← * = abstract
│  + get_balance(): float        │
└────────────────────────────────┘
             ▲
    ┌────────┴────────┐
    │                 │
┌───────────────┐  ┌───────────────────┐
│SavingsAccount │  │  CheckingAccount  │
├───────────────┤  ├───────────────────┤
│+interest_rate │  │+ overdraft_fee    │
├───────────────┤  ├───────────────────┤
│+ withdraw()   │  │+ withdraw()       │
│+ add_interest │  │+ charge_fee()     │
└───────────────┘  └───────────────────┘

┌─────────────────────┐
│        Bank         │        "1" ────── "1..*" Account
├─────────────────────┤
│+ name: str          │
│+ accounts: list     │
├─────────────────────┤
│+ add_account()      │
│+ find_account(): str│
└─────────────────────┘
```

## 15.2 Full Python Implementation

```python
from abc import ABC, abstractmethod
from typing import List, Optional

# ─── Abstract Base Class ───────────────────────────────────────────────────

class Account(ABC):
    """
    Abstract base class — defines the interface for ALL account types.
    Cannot be instantiated directly.
    """

    def __init__(self, number: str, owner: str, initial_balance: float = 0):
        self.number = number        # Public: account number (e.g., "ACC-001")
        self.owner  = owner         # Public: account owner's name
        self._balance = initial_balance  # Protected: internal balance storage

    def deposit(self, amount: float) -> None:
        """Add money to the account — shared by ALL account types."""
        if amount > 0:
            self._balance += amount
            print(f"  Deposited ${amount:.2f} → Balance: ${self._balance:.2f}")
        else:
            print("  Deposit amount must be positive.")

    @abstractmethod
    def withdraw(self, amount: float) -> bool:
        """
        Withdraw money — MUST be implemented by each subclass.
        Each account type has different withdrawal rules.
        Returns True if successful, False otherwise.
        """
        pass

    def get_balance(self) -> float:
        """Safe read-only access to the balance."""
        return self._balance

    def __str__(self):
        return f"[{self.number}] {self.owner} — Balance: ${self._balance:.2f}"

    def __repr__(self):
        return f"Account(number='{self.number}', owner='{self.owner}', balance={self._balance})"


# ─── Concrete Class 1: SavingsAccount ─────────────────────────────────────

class SavingsAccount(Account):
    """
    Savings Account: cannot overdraw (no spending more than you have).
    Earns interest.
    """

    def __init__(self, number: str, owner: str,
                 initial_balance: float = 0, interest_rate: float = 0.01):
        # Call parent's __init__ — sets number, owner, _balance
        super().__init__(number, owner, initial_balance)
        self.interest_rate = interest_rate  # e.g., 0.01 = 1%

    def withdraw(self, amount: float) -> bool:
        """Can only withdraw up to the available balance."""
        if amount > 0 and amount <= self._balance:
            self._balance -= amount
            print(f"  Withdrew ${amount:.2f} → Balance: ${self._balance:.2f}")
            return True
        print(f"  Cannot withdraw ${amount:.2f} — insufficient funds.")
        return False

    def add_interest(self) -> None:
        """Apply the interest rate to the current balance."""
        interest = self._balance * self.interest_rate
        self._balance += interest
        print(f"  Interest applied: +${interest:.2f} → Balance: ${self._balance:.2f}")


# ─── Concrete Class 2: CheckingAccount ────────────────────────────────────

class CheckingAccount(Account):
    """
    Checking Account: allows overdrafts, but charges a fee.
    """

    def __init__(self, number: str, owner: str,
                 initial_balance: float = 0, overdraft_fee: float = 25.0):
        super().__init__(number, owner, initial_balance)
        self.overdraft_fee = overdraft_fee

    def withdraw(self, amount: float) -> bool:
        """Allows overdraft — but charges a fee if balance goes negative."""
        if amount > 0:
            self._balance -= amount
            print(f"  Withdrew ${amount:.2f} → Balance: ${self._balance:.2f}")
            if self._balance < 0:
                # Balance went negative — apply overdraft fee
                self.charge_fee()
            return True
        print("  Withdrawal amount must be positive.")
        return False

    def charge_fee(self) -> None:
        """Penalize overdraft."""
        self._balance -= self.overdraft_fee
        print(f"  Overdraft fee charged: -${self.overdraft_fee:.2f} → Balance: ${self._balance:.2f}")


# ─── Bank Class (Aggregation: Bank has many Accounts) ─────────────────────

class Bank:
    """
    The Bank aggregates Account objects.
    Accounts are created externally and passed in — aggregation, not composition.
    """

    def __init__(self, name: str):
        self.name     = name
        self.accounts: List[Account] = []   # Holds references to accounts

    def add_account(self, account: Account) -> None:
        """Register an account with this bank."""
        self.accounts.append(account)
        print(f"  Account {account.number} registered at {self.name}.")

    def find_account(self, number: str) -> Optional[Account]:
        """Search for an account by number. Returns None if not found."""
        for account in self.accounts:
            if account.number == number:
                return account
        return None

    def __str__(self):
        return f"Bank: {self.name} ({len(self.accounts)} accounts)"


# ─── Demo ──────────────────────────────────────────────────────────────────

print("=" * 50)
print("  BANKING SYSTEM DEMO")
print("=" * 50)

# Create the bank
bank = Bank("BCIT Credit Union")

# Create accounts (externally — aggregation pattern)
savings  = SavingsAccount("ACC-001", "Alice Chen",   initial_balance=1000.0, interest_rate=0.03)
checking = CheckingAccount("ACC-002", "Bob Smith",   initial_balance=200.0,  overdraft_fee=35.0)

# Register accounts with the bank
print("\n[ Registering Accounts ]")
bank.add_account(savings)
bank.add_account(checking)

# Transactions on savings account
print("\n[ Alice's Savings Account ]")
print(savings)
savings.deposit(500.0)
savings.withdraw(200.0)
savings.withdraw(2000.0)   # Should fail — insufficient funds
savings.add_interest()

# Transactions on checking account
print("\n[ Bob's Checking Account ]")
print(checking)
checking.deposit(50.0)
checking.withdraw(400.0)   # Overdraft — triggers fee

# Find an account
print("\n[ Looking Up Account ACC-001 ]")
found = bank.find_account("ACC-001")
if found:
    print(f"  Found: {found}")

print("\n[ Final Bank Status ]")
print(bank)
```

## 15.3 Step-by-Step Runtime Flow

```
Program Start
     │
     ▼
Bank("BCIT Credit Union") created
     │
     ▼
SavingsAccount("ACC-001", "Alice", 1000.0) created
  → super().__init__() sets number, owner, _balance=1000.0
  → self.interest_rate = 0.03

CheckingAccount("ACC-002", "Bob", 200.0) created
  → super().__init__() sets number, owner, _balance=200.0
  → self.overdraft_fee = 35.0
     │
     ▼
bank.add_account(savings)   → accounts list = [savings]
bank.add_account(checking)  → accounts list = [savings, checking]
     │
     ▼
savings.deposit(500.0)
  → amount > 0? YES
  → _balance = 1000.0 + 500.0 = 1500.0

savings.withdraw(200.0)
  → 200 <= 1500? YES
  → _balance = 1500.0 - 200.0 = 1300.0

savings.withdraw(2000.0)
  → 2000 <= 1300? NO
  → Prints "insufficient funds"

savings.add_interest()
  → interest = 1300.0 * 0.03 = 39.0
  → _balance = 1300.0 + 39.0 = 1339.0
     │
     ▼
checking.withdraw(400.0)
  → amount > 0? YES
  → _balance = 250.0 - 400.0 = -150.0
  → _balance < 0? YES → charge_fee()
      → _balance = -150.0 - 35.0 = -185.0
```

## 15.4 Output

```
==================================================
  BANKING SYSTEM DEMO
==================================================

[ Registering Accounts ]
  Account ACC-001 registered at BCIT Credit Union.
  Account ACC-002 registered at BCIT Credit Union.

[ Alice's Savings Account ]
[ACC-001] Alice Chen — Balance: $1000.00
  Deposited $500.00 → Balance: $1500.00
  Withdrew $200.00 → Balance: $1300.00
  Cannot withdraw $2000.00 — insufficient funds.
  Interest applied: +$39.00 → Balance: $1339.00

[ Bob's Checking Account ]
[ACC-002] Bob Smith — Balance: $200.00
  Deposited $50.00 → Balance: $250.00
  Withdrew $400.00 → Balance: -$150.00
  Overdraft fee charged: -$35.00 → Balance: -$185.00

[ Looking Up Account ACC-001 ]
  Found: [ACC-001] Alice Chen — Balance: $1339.00

[ Final Bank Status ]
Bank: BCIT Credit Union (2 accounts)
```

---

## Quick Reference Cheat Sheet

```
OOP PILLARS
───────────
Modelization   → Describe real-world things with just the attributes you need
Encapsulation  → Bundle data + methods; protect with _ or __ conventions
Abstraction    → Show simple interface; hide complex internals (use @property)
Inheritance    → Child IS-A parent; use super().__init__() always
Polymorphism   → Same method name, different behavior per class

UML NOTATION
────────────
+ public     # protected    - private
─────  association
──◇─── aggregation (has-a, weak)
──◆─── composition (part-of, strong)
───▷   inheritance (is-a)
<<abstract>>  cannot be instantiated; methods marked with *

MULTIPLICITY
────────────
1       exactly one
0..1    zero or one
*       zero or more
1..*    one or more

PYTHON CONVENTIONS
──────────────────
self._name    protected (use single underscore in most cases)
self.__name   private — name mangled to self._ClassName__name
@property     turn method into virtual attribute (getter)
@x.setter     add write access to a property with validation
super()       access parent class methods safely
ABC + @abstractmethod   enforce a contract on subclasses
```

---

*End of Lesson — Object-Oriented Programming in Python & UML Class Diagrams*
