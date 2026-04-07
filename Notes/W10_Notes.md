# Inheritance and Abstract Base Classes in Python — Complete Study Guide

> **Prerequisites:** Basic understanding of Python classes, `__init__`, `self`, instance attributes, and methods.
> These notes build on introductory OOP concepts. If terms like "class", "object", or "method" are unfamiliar, review introductory OOP material first.

---

## Table of Contents

1. [Inheritance](#1-inheritance)
2. [`super().__init__()` — Calling the Parent Constructor](#2-super__init__--calling-the-parent-constructor)
3. [Method Overriding](#3-method-overriding)
4. [Extending a Parent Method with `super()`](#4-extending-a-parent-method-with-super)
5. [Polymorphism](#5-polymorphism)
6. [`isinstance()` — Checking Object Types at Runtime](#6-isinstance--checking-object-types-at-runtime)
7. [Abstract Base Classes (ABCs)](#7-abstract-base-classes-abcs)
8. [Multiple Inheritance and Mixins](#8-multiple-inheritance-and-mixins)
9. [Summary Cheat Sheet](#9-summary-cheat-sheet)

---

## 1. Inheritance

### What Is Inheritance?

**Inheritance** is a mechanism in object-oriented programming that allows a new class (called the **subclass**, **child class**, or **derived class**) to automatically receive — that is, *inherit* — all of the attributes and methods from an existing class (called the **superclass**, **parent class**, or **base class**).

Once a subclass inherits from a parent, it can do any combination of the following:

- **Use** the parent's attributes and methods as-is, without writing any new code.
- **Add** entirely new attributes and methods that only the subclass has.
- **Override** (replace) specific parent methods with its own custom version.
- **Extend** parent methods by running the parent's original code and then adding extra steps.

### Syntax

```python
class Parent:
    # Parent class with shared attributes and methods
    ...

class Child(Parent):
    # Child inherits EVERYTHING from Parent
    # The parentheses with the parent's name is what triggers inheritance
    ...
```

**Key detail:** The parentheses after `Child` are what tell Python "this class inherits from `Parent`." Without those parentheses (or with empty ones), no inheritance takes place.

### Why Does Inheritance Exist?

Inheritance exists to solve a very real and common problem: **code duplication**.

Imagine you are building a system that manages different kinds of animals — `Dog`, `Cat`, and `Bird`. Without inheritance, every single class would need its own copy of shared data like `name` and `age`, and shared behaviours like `eat()` and `sleep()`:

```python
# WITHOUT inheritance — lots of duplicated code (BAD)
class Dog:
    def __init__(self, name, age, breed):
        self.name = name       # duplicated
        self.age = age         # duplicated
        self.breed = breed

    def eat(self):             # duplicated
        print(f"{self.name} is eating.")

    def sleep(self):           # duplicated
        print(f"{self.name} is sleeping.")


class Cat:
    def __init__(self, name, age, indoor):
        self.name = name       # duplicated again!
        self.age = age         # duplicated again!
        self.indoor = indoor

    def eat(self):             # duplicated again!
        print(f"{self.name} is eating.")

    def sleep(self):           # duplicated again!
        print(f"{self.name} is sleeping.")
```

This violates the **DRY (Don't Repeat Yourself) principle**. If you later discover a bug in `eat()`, you must fix it in `Dog`, `Cat`, `Bird`, and every other class that copied it. Miss one, and you have an inconsistent, buggy system.

Inheritance solves this by letting you write the shared logic **once** in a parent class. Every child automatically gets it:

```python
# WITH inheritance — shared logic lives in one place (GOOD)
class Animal:
    def __init__(self, name: str, age: int):
        self._name = name
        self._age = age

    def eat(self):
        print(f"{self._name} is eating.")

    def sleep(self):
        print(f"{self._name} is sleeping.")


class Dog(Animal):          # Dog IS-A Animal
    def __init__(self, name: str, age: int, breed: str):
        super().__init__(name, age)   # let Animal set up _name and _age
        self._breed = breed           # Dog adds its own unique attribute


class Cat(Animal):          # Cat IS-A Animal
    def __init__(self, name: str, age: int, indoor: bool = True):
        super().__init__(name, age)   # let Animal set up _name and _age
        self._indoor = indoor         # Cat adds its own unique attribute
```

Now `eat()` and `sleep()` exist only in `Animal`. Fix a bug there, and every child (`Dog`, `Cat`, any future animal) automatically gets the fix.

### The IS-A Relationship

Inheritance models **IS-A** relationships — the same hierarchical relationships we use in everyday language:

- "A Dog **is an** Animal"
- "A Cat **is an** Animal"
- "An Employee **is a** Person"

If the phrase "X is a Y" sounds natural in English, then `class X(Y)` is probably a correct use of inheritance. If it does **not** sound natural (e.g., "A Car is an Engine" — no, a car *has* an engine), then inheritance is the wrong tool, and you should use **composition** instead (storing one object inside another as an attribute).

### Class Diagram

```
         ┌──────────────────────────────────┐
         │            Animal                 │
         │──────────────────────────────────│
         │  - _name: str                     │
         │  - _age: int                      │
         │──────────────────────────────────│
         │  + __init__(name, age)            │
         │  + eat()                          │
         │  + sleep()                        │
         │  + speak() -> str                 │
         └──────────┬───────────────────────┘
                    │ inherits from
          ┌─────────┴──────────┐
          ▼                    ▼
┌──────────────────┐  ┌──────────────────┐
│       Dog        │  │       Cat        │
│──────────────────│  │──────────────────│
│  - _breed: str   │  │  - _indoor: bool │
│──────────────────│  │──────────────────│
│  + __init__(     │  │  + __init__(     │
│    name, age,    │  │    name, age,    │
│    breed)        │  │    indoor)       │
│  + speak() -> str│  │  + speak() -> str│
│  + fetch()       │  │  + purr()        │
└──────────────────┘  └──────────────────┘
```

### What My Professor Didn't Explain

- **All Python classes implicitly inherit from `object`.** Even if you write `class Animal:` with no parentheses, Python silently treats it as `class Animal(object):`. The `object` base class provides default implementations of dunder methods like `__str__`, `__repr__`, and `__eq__`.
- **Inheritance is transitive.** If `Dog` inherits from `Animal` and `Animal` inherits from `object`, then `Dog` also inherits from `object`. A `Dog` instance is simultaneously a `Dog`, an `Animal`, and an `object`.
- **Private name-mangling still applies.** Attributes with a double underscore prefix (e.g., `self.__secret`) are name-mangled and are *not* directly accessible by subclasses. Single underscore attributes (e.g., `self._name`) are a *convention* meaning "treat this as internal," but they are fully accessible by subclasses.

### Warnings and Best Practices

- **Prefer shallow hierarchies.** Deep chains like `A → B → C → D → E` become extremely hard to debug. If your hierarchy is more than 2–3 levels deep, consider using composition instead.
- **Favour the IS-A test.** Before creating an inheritance relationship, always ask: "Is the child truly a *type of* the parent?" If not, use composition (storing one object as an attribute of another).
- **Don't inherit just to reuse code.** Inheritance should represent a logical relationship, not just a way to avoid rewriting a useful method. If two classes share behaviour but aren't logically related, extract that behaviour into a utility function or a mixin instead.

---

## 2. `super().__init__()` — Calling the Parent Constructor

### What Is `super()`?

`super()` is a built-in Python function that returns a **temporary proxy object** — essentially a reference to the parent class. Through this proxy, you can call any of the parent's methods, most commonly the `__init__` constructor.

When you write `super().__init__(...)` inside a child class's `__init__`, you are explicitly telling Python: *"Before I set up my own child-specific data, go run the parent's `__init__` first so that the foundational attributes get created."*

### Why Is This Necessary?

Here is a critical detail that trips up many beginners: **defining `__init__` in a child class completely replaces the parent's `__init__`**. Python does not automatically call the parent's constructor for you. If the parent's `__init__` creates essential attributes (like `self._name` and `self._email`), and you skip `super().__init__()`, those attributes simply will not exist on your object. The first time you (or any inherited method) tries to access them, Python raises an `AttributeError`.

### Syntax

```python
class Child(Parent):
    def __init__(self, parent_param1, parent_param2, child_param1):
        super().__init__(parent_param1, parent_param2)  # ← Runs Parent.__init__
        self._child_param1 = child_param1               # ← Child's own setup
```

**Rule of thumb:** Always call `super().__init__(...)` as the **first line** of a subclass's `__init__`. This ensures the parent's attributes are set up before the child tries to use or build upon them.

### Use Case: `Person` → `Employee`

```python
class Person:
    def __init__(self, name: str, email: str):
        self._name = name
        self._email = email


class Employee(Person):
    def __init__(self, name: str, email: str, employee_id: str, salary: float):
        super().__init__(name, email)    # Person sets self._name and self._email
        self._employee_id = employee_id  # Employee adds its own attributes
        self._salary = salary
```

**What happens step by step when you create an `Employee`:**

```python
emp = Employee("Alice", "alice@example.com", "E1042", 75000.0)
```

1. Python calls `Employee.__init__("Alice", "alice@example.com", "E1042", 75000.0)`.
2. The first line inside, `super().__init__("Alice", "alice@example.com")`, jumps to `Person.__init__`.
3. `Person.__init__` creates `self._name = "Alice"` and `self._email = "alice@example.com"`.
4. Control returns to `Employee.__init__`, which creates `self._employee_id = "E1042"` and `self._salary = 75000.0`.
5. The `emp` object now has all four attributes: `_name`, `_email`, `_employee_id`, and `_salary`.

### What Happens If You Forget `super().__init__()`?

```python
# WRONG — missing super().__init__()
class BrokenEmployee(Person):
    def __init__(self, name: str, email: str, employee_id: str):
        self._employee_id = employee_id
        # self._name and self._email are NEVER created!
```

```python
broken = BrokenEmployee("Bob", "bob@co.com", "E999")
print(broken._employee_id)  # Works fine: "E999"
print(broken._name)         # AttributeError: 'BrokenEmployee' object has no attribute '_name'
```

The object is "half-built." It has the child's attributes but is missing all of the parent's attributes. Any method that tries to access `self._name` or `self._email` will crash.

### What My Professor Didn't Explain

- **`super()` is not limited to `__init__`.** You can use `super()` to call *any* parent method, not just the constructor. For example, `super().speak()` calls the parent's `speak()` method. This is covered in more detail in Section 4.
- **The older Python 2 syntax was `super(ClassName, self).__init__()`.** In Python 3, the shorter `super().__init__()` works identically and is the modern standard. If you see the longer form in older code or tutorials, know that it does the same thing.
- **If the parent class has no `__init__` (or its `__init__` takes no parameters beyond `self`)**, you still do not *need* to call `super().__init__()` — but it is considered good practice to do so, because it makes your code robust if the parent later adds an `__init__`.

### Warnings and Best Practices

- **Always call `super().__init__()` first**, before any of the child's own attribute assignments. Doing child setup before the parent's setup can lead to subtle bugs where child code depends on not-yet-created parent attributes.
- **Pass only the arguments the parent expects.** Look at the parent's `__init__` signature to know which parameters it needs. Do not pass child-specific arguments to `super().__init__()` — the parent will not know what to do with them.

---

## 3. Method Overriding

### What Is Method Overriding?

**Method overriding** occurs when a subclass defines a method with the **exact same name** as a method in its parent class. When you call that method on an instance of the subclass, Python uses the **subclass's version** instead of the parent's.

Think of it as the child saying: *"I know the parent has a `speak()` method, but I want to do it my own way."*

### Why Does Method Overriding Exist?

Inheriting methods is powerful, but not every child class should behave identically to its parent. Overriding gives subclasses the flexibility to **customize or completely replace** inherited behaviour while keeping the same method name (interface).

This is essential because it allows the rest of your code to call `animal.speak()` on *any* animal without needing to know (or care about) which specific type of animal it is. The correct version runs automatically.

There are two common patterns for why a parent defines a method that children are expected to override:

1. **The parent defines a placeholder that deliberately raises an error.** This signals "every child MUST provide its own version." (This manual approach is later replaced by the more robust ABC pattern in Section 7.)
2. **The parent defines a generic default implementation** that some children may want to specialize.

### How Python Decides Which Method to Run

When you call `obj.some_method()`, Python searches for `some_method` in this order:

1. The object's own class (the most specific subclass).
2. The parent class.
3. The grandparent class.
4. ... all the way up to `object`.

Python stops at the **first match** it finds. This is why the subclass's version "wins" — it is found first.

### Use Case: Overriding `speak()`

```python
class Animal:
    def __init__(self, name: str, age: int):
        self._name = name
        self._age = age

    def speak(self) -> str:
        raise NotImplementedError("Subclasses must implement speak()")


class Dog(Animal):
    def __init__(self, name: str, age: int, breed: str):
        super().__init__(name, age)
        self._breed = breed

    def speak(self) -> str:          # overrides Animal.speak()
        return "Woof!"


class Cat(Animal):
    def __init__(self, name: str, age: int, indoor: bool = True):
        super().__init__(name, age)
        self._indoor = indoor

    def speak(self) -> str:          # overrides Animal.speak()
        return "Meow!"
```

**Testing the override:**

```python
dog = Dog("Rex", 3, "Labrador")
cat = Cat("Whiskers", 5)

print(dog.speak())   # "Woof!"  ← Python finds Dog.speak() first, uses it
print(cat.speak())   # "Meow!"  ← Python finds Cat.speak() first, uses it
```

**What would happen if we called `speak()` on a plain `Animal`?**

```python
generic = Animal("Thing", 1)
generic.speak()  # Raises NotImplementedError: "Subclasses must implement speak()"
```

The parent's `speak()` is intentionally broken — it exists as a placeholder that forces children to provide their own version.

### What My Professor Didn't Explain

- **You do not need any special keyword or decorator to override a method.** Simply defining a method in the child with the same name as the parent's method is all it takes. Python uses the "first match wins" search order described above.
- **The `NotImplementedError` pattern is a manual, informal contract.** It only crashes at runtime — when `speak()` is actually called. If you forget to override `speak()` in a new subclass, Python will not warn you until that line executes. For a stricter, compile-time-like guarantee, use **Abstract Base Classes** (Section 7).
- **The overriding method should have a compatible signature.** If the parent's `speak()` takes no arguments (beyond `self`) and returns a `str`, the child's `speak()` should do the same. Changing the signature can lead to confusing bugs.

### Warnings and Best Practices

- **Beware of typos in method names.** If you write `def speek(self)` instead of `def speak(self)` in the child, you have *not* overridden anything — you have created a brand new method called `speek`, and the parent's `speak()` (with the `NotImplementedError`) will still be called. Python will not warn you about this mistake.
- **Overriding `__init__` without calling `super().__init__()` is the most common override-related bug.** See Section 2 for details.

---

## 4. Extending a Parent Method with `super()`

### What Is Method Extension?

Sometimes you don't want to completely *replace* the parent's method — you want to **keep what the parent does and add more on top of it**. This is called **extending** a method.

The pattern is:

1. Call `super().method_name()` to execute the parent's original version.
2. Add the subclass's additional logic before or after that call.

### Why Use Extension Instead of Full Override?

Extension follows the **DRY (Don't Repeat Yourself) principle**. If the parent's method already does something useful (e.g., formatting a person's name and email), there is no reason for the child to rewrite that logic. The child should leverage the parent's work and only add the parts that are unique to itself.

This also means that if the parent's logic is later updated or bug-fixed, the child automatically benefits without any code changes.

### Syntax Pattern

```python
class Child(Parent):
    def some_method(self):
        parent_result = super().some_method()  # Step 1: Run parent's version
        # Step 2: Add child-specific logic
        return f"{parent_result} + extra stuff from Child"
```

### Use Case: Extending `introduce()`

```python
class Person:
    def __init__(self, name: str, email: str):
        self._name = name
        self._email = email

    def introduce(self) -> str:
        return f"Hi, I'm {self._name} (email: {self._email})"


class Employee(Person):
    def __init__(self, name: str, email: str, employee_id: str, salary: float):
        super().__init__(name, email)
        self._employee_id = employee_id
        self._salary = salary

    def introduce(self) -> str:
        base = super().introduce()     # "Hi, I'm Alice (email: alice@example.com)"
        return f"{base} | Employee ID: {self._employee_id}"
```

**Testing it:**

```python
emp = Employee("Alice", "alice@example.com", "E1042", 75000.0)
print(emp.introduce())
```

**Output:**

```
Hi, I'm Alice (email: alice@example.com) | Employee ID: E1042
```

**What happened step by step:**

1. `emp.introduce()` is called. Python finds `Employee.introduce()` first.
2. Inside `Employee.introduce()`, `super().introduce()` calls `Person.introduce()`.
3. `Person.introduce()` returns `"Hi, I'm Alice (email: alice@example.com)"` — this is stored in `base`.
4. `Employee.introduce()` appends `" | Employee ID: E1042"` to the result and returns the combined string.

### Override vs. Extend — When to Use Which

| Scenario | Technique | Example |
|---|---|---|
| The child needs completely different behaviour | **Full override** (no `super()` call) | `Dog.speak()` returns `"Woof!"` — no parent logic needed |
| The child needs the parent's behaviour plus more | **Extension** (call `super()` then add) | `Employee.introduce()` reuses `Person.introduce()` |
| The parent's method is a placeholder / raises an error | **Full override** | `Animal.speak()` raises `NotImplementedError` |

### What My Professor Didn't Explain

- **You can call `super()` at any point in the method**, not just the first line. You might do some child-specific preparation first, then call `super()`, then do more child-specific work afterward. The "first line" rule only strictly applies to `super().__init__()` in constructors.
- **Extension works with any method, not just `__init__`.** You can extend `__str__`, `__repr__`, `save()`, `validate()`, or any other method.
- **The `super()` call returns whatever the parent's method returns.** If the parent method returns `None` (as with `print`-based methods), there is nothing to capture — you just call `super().method()` for its side effects.

---

## 5. Polymorphism

### What Is Polymorphism?

**Polymorphism** (from Greek: *poly* = many, *morph* = form) means that different classes can be used through the **same interface** (i.e., the same method name), and each class responds in its own appropriate way.

In practical terms: you can call `.speak()` on any object that has a `speak()` method, and the correct version runs automatically based on the object's actual class. You do not need to check what type the object is or write separate code paths for each type.

### Why Does Polymorphism Matter?

Polymorphism makes your code:

- **Flexible:** A single loop or function can work with objects of many different types.
- **Extensible (future-proof):** If you add a new class (`Bird`) months later that also has a `speak()` method, all your existing code that calls `.speak()` will work with `Bird` instances *without any modifications*.
- **Clean:** You avoid long chains of `if isinstance(animal, Dog): ... elif isinstance(animal, Cat): ...` checks.

This is widely considered one of the most powerful benefits of object-oriented programming.

### Use Case: A Mixed List of Animals

```python
class Animal:
    def __init__(self, name: str, age: int):
        self._name = name
        self._age = age

    def speak(self) -> str:
        raise NotImplementedError("Subclasses must implement speak()")


class Dog(Animal):
    def __init__(self, name: str, age: int, breed: str):
        super().__init__(name, age)
        self._breed = breed

    def speak(self) -> str:
        return "Woof!"


class Cat(Animal):
    def __init__(self, name: str, age: int, indoor: bool = True):
        super().__init__(name, age)
        self._indoor = indoor

    def speak(self) -> str:
        return "Meow!"
```

```python
animals = [
    Dog("Rex", 3, "Labrador"),
    Cat("Whiskers", 5),
    Dog("Buddy", 2, "Poodle"),
]

# Same method call, different behaviour — this is polymorphism
for animal in animals:
    print(f"{animal._name} says: {animal.speak()}")
```

**Output:**

```
Rex says: Woof!
Whiskers says: Meow!
Buddy says: Woof!
```

Notice that the `for` loop does not contain any `if` statements checking whether each animal is a `Dog` or a `Cat`. It simply calls `animal.speak()`, and Python figures out the correct version at runtime based on the object's actual type.

### Another Use Case: Employee Bonus Calculation

The same principle applies to any class hierarchy:

```python
class Employee:
    def __init__(self, name: str, salary: float):
        self._name = name
        self._salary = salary

    def calculate_bonus(self) -> float:
        raise NotImplementedError


class Manager(Employee):
    def calculate_bonus(self) -> float:
        return self._salary * 0.20   # Managers get 20% bonus


class SalesRep(Employee):
    def calculate_bonus(self) -> float:
        return self._salary * 0.15   # Sales reps get 15% bonus
```

```python
employees = [
    Manager("Alice", 90000),
    SalesRep("Bob", 50000),
]

for emp in employees:
    print(f"{emp._name}: bonus = ${emp.calculate_bonus():.2f}")
```

**Output:**

```
Alice: bonus = $18000.00
Bob: bonus = $7500.00
```

If you later add an `Engineer` class with its own `calculate_bonus()`, the loop above will handle it automatically with zero changes.

### What My Professor Didn't Explain

- **Python uses "duck typing."** The phrase comes from: *"If it walks like a duck and quacks like a duck, then it's a duck."* In Python, polymorphism does not require inheritance at all. If *any* object has a `.speak()` method — even if it does not inherit from `Animal` — you can call `.speak()` on it and it will work. Inheritance is one way to guarantee a common interface, but Python does not strictly require it.
- **Polymorphism works with any method, operator, or dunder.** It is not limited to custom methods like `speak()`. For example, the `+` operator calls `__add__()` under the hood, and different classes (integers, strings, lists) each implement `__add__()` differently. That is polymorphism too.
- **Accessing `animal._name` directly (as in the example) works but is not ideal practice.** The leading underscore signals "this is internal." In production code, you would typically provide a public method or property like `animal.name` to access it. The examples here use `_name` directly for simplicity.

### Warnings and Best Practices

- **Ensure all subclasses implement the expected methods.** If you forget to implement `speak()` in a new subclass, you will get a `NotImplementedError` at runtime. Use Abstract Base Classes (Section 7) to catch this mistake at object creation time.
- **Keep method signatures consistent across subclasses.** If `Dog.speak()` takes no arguments, `Cat.speak()` should also take no arguments. Inconsistent signatures break polymorphism because calling code cannot use a uniform interface.

---

## 6. `isinstance()` — Checking Object Types at Runtime

### What Is `isinstance()`?

`isinstance(obj, ClassName)` is a built-in Python function that returns `True` if `obj` is an instance of `ClassName` **or any subclass of `ClassName`**. Otherwise, it returns `False`.

### Syntax

```python
isinstance(obj, SomeClass)            # True if obj is SomeClass or a subclass of it
isinstance(obj, (ClassA, ClassB))     # True if obj is an instance of EITHER ClassA or ClassB
```

**The second form** — passing a tuple of classes — is a convenient shorthand for checking multiple types at once. It is equivalent to `isinstance(obj, ClassA) or isinstance(obj, ClassB)`.

### Why Does `isinstance()` Exist?

While polymorphism encourages treating all objects the same way, sometimes you genuinely need to check an object's type:

- **To access subclass-specific attributes or methods.** For example, only `Dog` objects have a `_breed` attribute. Calling `animal._breed` on a `Cat` would crash. You need to confirm the object is a `Dog` first.
- **To guard dunder methods against incompatible types.** For example, comparing an `Employee` to an integer does not make sense. The `__eq__` method should return `NotImplemented` if the other object is not a compatible type.
- **To route different subclass types to different processing paths** when polymorphism alone is not sufficient.

### Use Case: Accessing Subclass-Specific Attributes Safely

```python
animals = [Dog("Rex", 3, "Labrador"), Cat("Whiskers", 5)]

for animal in animals:
    print(isinstance(animal, Animal))  # True for both (Dog and Cat are Animals)
    print(isinstance(animal, Dog))     # True only for Rex

    if isinstance(animal, Dog):
        # Safe: we know this is a Dog, so _breed exists
        print(f"{animal._name}'s breed: {animal._breed}")
```

**Output:**

```
True
True
Rex's breed: Labrador
True
False
```

### Use Case: Guarding `__eq__` in Dunder Methods

```python
class Person:
    def __init__(self, name: str, email: str):
        self._name = name
        self._email = email

    def __eq__(self, other: object) -> bool:
        if not isinstance(other, Person):
            return NotImplemented   # tells Python: "I can't compare with this type"
        return self._email == other._email  # same email → same person
```

**What `return NotImplemented` does:**

When `__eq__` returns the special singleton `NotImplemented` (not `NotImplementedError` — this is a *value*, not an exception), Python knows that this class cannot handle the comparison. Python then tries the *other* object's `__eq__` method. If neither side can handle it, the comparison falls back to identity comparison (`is`), which returns `False` for different objects.

```python
p = Person("Alice", "alice@co.com")
print(p == Person("Alice Clone", "alice@co.com"))  # True (same email)
print(p == Person("Bob", "bob@co.com"))             # False (different email)
print(p == 42)                                       # False (NotImplemented → fallback)
```

### `isinstance()` vs. `type()`

You might wonder why we don't just use `type(obj) == SomeClass`. The critical difference:

| Check | Subclasses Included? | Recommended? |
|---|---|---|
| `isinstance(obj, Animal)` | Yes — returns `True` for `Dog`, `Cat`, etc. | **Yes** |
| `type(obj) == Animal` | No — returns `True` *only* for `Animal` exactly | Rarely |

Since inheritance creates IS-A relationships (`Dog` IS-A `Animal`), you almost always want `isinstance()`, which respects that hierarchy.

### What My Professor Didn't Explain

- **`isinstance()` works with abstract base classes too.** If `Animal` is an ABC, `isinstance(dog, Animal)` still returns `True` for any concrete subclass.
- **`NotImplemented` is NOT the same as `NotImplementedError`.** `NotImplemented` is a special return *value* used in dunder methods to signal "I can't handle this." `NotImplementedError` is an *exception* you raise in placeholder methods. Confusing them is a common beginner mistake.
- **Overusing `isinstance()` is a code smell.** If you find yourself writing long chains of `if isinstance(obj, X): ... elif isinstance(obj, Y): ...`, it often means your class hierarchy needs better polymorphism. Let each class handle its own behaviour through overridden methods.

---

## 7. Abstract Base Classes (ABCs)

### What Is an Abstract Base Class?

An **Abstract Base Class (ABC)** is a special kind of class that serves as a **strict, enforceable blueprint**. It has two defining characteristics:

1. **It cannot be instantiated directly.** You cannot create an object from an ABC — you can only create objects from its concrete (non-abstract) subclasses.
2. **It declares one or more abstract methods** that every subclass **must** implement. If a subclass fails to implement even one abstract method, Python refuses to create an instance of that subclass.

Python's `abc` module provides the tools to create ABCs:

```python
from abc import ABC, abstractmethod
```

- **`ABC`**: A helper class you inherit from to make your class abstract.
- **`@abstractmethod`**: A decorator you place above any method that subclasses are required to implement.

### Why Do ABCs Exist?

Recall from Section 3 that the `NotImplementedError` pattern is a *manual, informal* contract. It only catches mistakes at runtime — and only when the missing method is actually called, which could be deep in production, months after the code was written.

ABCs upgrade this to a **formal, enforced contract**. The error is caught at **object creation time** — the instant you try to create an instance of a subclass that is missing a required method, Python raises a `TypeError`. You find the bug immediately, not later in production.

Think of an ABC like a legal contract template:

- The ABC defines *what* must exist (the method signatures).
- Each subclass signs the contract by providing the actual implementations.
- Python is the enforcer that refuses to let any subclass operate without fulfilling the contract.

### Syntax

```python
from abc import ABC, abstractmethod

class MyAbstractClass(ABC):          # Step 1: Inherit from ABC
    
    @abstractmethod                   # Step 2: Mark methods that MUST be overridden
    def required_method(self):
        pass

    def shared_method(self):          # Concrete methods are inherited normally
        print("This is shared by all subclasses.")
```

### Use Case: `Notification` as an ABC

Imagine you are building a notification system that supports Email, SMS, and Push notifications. Every notification type *must* be sendable — but the mechanics of sending differ for each channel.

```python
from abc import ABC, abstractmethod

class Notification(ABC):                # ← Abstract: cannot be instantiated
    def __init__(self, recipient: str, message: str):
        self._recipient = recipient
        self._message = message

    # Concrete method — shared by ALL subclasses, no override required
    def log(self):
        print(f"[LOG] Sending '{self._message}' to {self._recipient}")

    @abstractmethod                     # ← Every subclass MUST implement this
    def send(self) -> bool:
        pass
```

**What happens if you try to instantiate `Notification` directly?**

```python
n = Notification("user@example.com", "Hello!")
# TypeError: Can't instantiate abstract class Notification with abstract method send
```

Python stops you immediately. This is the power of ABCs — the error happens at creation time, not later when `send()` is called.

**Concrete subclasses that implement all abstract methods work fine:**

```python
class EmailNotification(Notification):
    def send(self) -> bool:
        print(f"Sending email to {self._recipient}: {self._message}")
        return True


class SMSNotification(Notification):
    def send(self) -> bool:
        print(f"Sending SMS to {self._recipient}: {self._message}")
        return True
```

```python
email = EmailNotification("user@example.com", "Welcome!")
email.log()     # [LOG] Sending 'Welcome!' to user@example.com
email.send()    # Sending email to user@example.com: Welcome!
```

**What happens if a subclass forgets to implement `send()`?**

```python
class BrokenNotification(Notification):
    pass  # Forgot to implement send()!

broken = BrokenNotification("user@example.com", "Hello!")
# TypeError: Can't instantiate abstract class BrokenNotification with abstract method send
```

Python catches the error before any damage is done.

### Abstract Properties

You can also make **properties** abstract by stacking `@property` and `@abstractmethod` (in that order). This forces every subclass to define those properties.

```python
class Notification(ABC):
    def __init__(self, recipient: str, message: str):
        self._recipient = recipient
        self._message = message

    @property
    @abstractmethod
    def channel(self) -> str:
        """The delivery channel label, e.g., 'Email' or 'SMS'."""

    @property
    @abstractmethod
    def max_length(self) -> int:
        """Maximum allowed message length for this channel."""

    @abstractmethod
    def send(self) -> bool:
        pass

    # Concrete method that USES the abstract properties
    def validate(self) -> bool:
        if len(self._message) > self.max_length:
            raise ValueError(
                f"{self.channel} messages cannot exceed {self.max_length} chars"
            )
        return True
```

**Key insight:** The concrete method `validate()` uses `self.channel` and `self.max_length` — both of which are abstract. Because the ABC *guarantees* every subclass will provide these properties, `validate()` can safely rely on them. This is the ABC contract in action.

**Implementing the abstract properties in a subclass:**

```python
class SMSNotification(Notification):
    @property
    def channel(self) -> str:
        return "SMS"

    @property
    def max_length(self) -> int:
        return 160

    def send(self) -> bool:
        self.validate()   # uses channel and max_length from this class
        print(f"Sending SMS to {self._recipient}: {self._message}")
        return True
```

```python
sms = SMSNotification("+1234567890", "Hello!")
sms.validate()  # True (5 chars < 160)
sms.send()      # Sending SMS to +1234567890: Hello!

long_sms = SMSNotification("+1234567890", "x" * 200)
long_sms.send()  # Raises ValueError: SMS messages cannot exceed 160 chars
```

**Decorator order matters:** It must be `@property` first, then `@abstractmethod` second. Reversing the order will cause unexpected behaviour.

### Abstract Methods with a Concrete Body

A common misconception is that abstract methods must have an empty body (`pass`). In fact, **abstract methods can contain real code**. Making the method abstract still forces every subclass to override it, but the parent's body provides shared logic that subclasses can access via `super()`.

```python
class Report(ABC):
    def __init__(self, title: str):
        self._title = title

    @abstractmethod
    def generate(self):
        # This body contains shared logic — subclasses call super().generate()
        print(f"=== {self._title} ===")
        print("Report generated\n")
```

```python
class SalesReport(Report):
    def __init__(self, title: str, total_sales: float):
        super().__init__(title)
        self._total_sales = total_sales

    def generate(self):
        super().generate()                           # prints the shared header
        print(f"Total Sales: ${self._total_sales:,.2f}")  # adds specific content


class InventoryReport(Report):
    def __init__(self, title: str, item_count: int):
        super().__init__(title)
        self._item_count = item_count

    def generate(self):
        super().generate()                           # prints the shared header
        print(f"Items in stock: {self._item_count}")  # adds specific content
```

```python
sales = SalesReport("Q4 Sales Summary", 1_250_000.00)
sales.generate()
```

**Output:**

```
=== Q4 Sales Summary ===
Report generated

Total Sales: $1,250,000.00
```

**The pattern:**

1. `@abstractmethod` forces every subclass to override `generate()` — they cannot skip it.
2. The parent's `generate()` body contains the shared header-printing logic.
3. Each subclass calls `super().generate()` to run the shared logic, then adds its own content.

This combines the enforcement of ABCs with the code-reuse of method extension.

### Class Diagram: Notification Hierarchy

```
        ┌──────────────────────────────────────────┐
        │         <<abstract>>                      │
        │          Notification                     │
        │──────────────────────────────────────────│
        │  - _recipient: str                        │
        │  - _message: str                          │
        │──────────────────────────────────────────│
        │  + log()                     (concrete)   │
        │  + validate() -> bool        (concrete)   │
        │  + send() -> bool            (abstract)   │
        │  + channel: str              (abstract)   │
        │  + max_length: int           (abstract)   │
        └──────────────┬───────────────────────────┘
                       │ inherits from
           ┌───────────┴────────────┐
           ▼                        ▼
┌──────────────────────┐  ┌──────────────────────┐
│  EmailNotification   │  │   SMSNotification    │
│──────────────────────│  │──────────────────────│
│  + send() -> bool    │  │  + send() -> bool    │
│  + channel: str      │  │  + channel: str      │
│  + max_length: int   │  │  + max_length: int   │
└──────────────────────┘  └──────────────────────┘
```

### ABC vs. NotImplementedError — Comparison

| Feature | `NotImplementedError` Pattern | ABC with `@abstractmethod` |
|---|---|---|
| When is the error caught? | At runtime, when the method is called | At object creation time (`TypeError`) |
| Can you forget to override? | Yes — no warning until the method runs | No — Python refuses to create the object |
| Requires import? | No | Yes (`from abc import ABC, abstractmethod`) |
| Can the parent be instantiated? | Yes (unless manually guarded) | No — automatically prevented |
| Best for | Quick prototypes, small projects | Large projects, team codebases, enforced contracts |

### What My Professor Didn't Explain

- **An ABC can have zero abstract methods.** If you inherit from `ABC` but define no abstract methods, the class still cannot be instantiated directly (it is still abstract). However, this is unusual and defeats the main purpose of ABCs.
- **You can combine `@abstractmethod` with `@staticmethod` or `@classmethod`** by stacking decorators (e.g., `@classmethod` then `@abstractmethod`). The `@abstractmethod` must always be the innermost (bottom) decorator.
- **ABCs are used extensively in Python's standard library.** For example, `collections.abc` defines abstract classes like `Iterable`, `Mapping`, `Sequence`, etc. When you implement these, Python guarantees your custom class behaves like a proper collection.

### Warnings and Best Practices

- **Decorator order for abstract properties:** Always `@property` on top, `@abstractmethod` below. Reversing them leads to errors.
- **Don't create ABCs for everything.** ABCs add rigidity. Use them when you have a clear hierarchy where enforcing a contract prevents real bugs (e.g., a plugin system, a notification framework). For small scripts or simple class hierarchies, `NotImplementedError` or duck typing may be sufficient.
- **ABCs should define *what*, not *how*.** The abstract methods declare what the subclass must do, but the subclass decides how to do it.

---

## 8. Multiple Inheritance and Mixins

### What Is Multiple Inheritance?

**Multiple inheritance** means a class can inherit from **more than one parent class**:

```python
class Child(ParentA, ParentB):
    ...
```

`Child` inherits all attributes and methods from *both* `ParentA` and `ParentB`.

### What Is a Mixin?

A **mixin** is a small, focused class designed to be "mixed into" other classes to add a single, specific piece of functionality. Mixins:

- Are **not meant to be instantiated on their own** (they are incomplete by design).
- Do **not represent an IS-A relationship** (a `LoggableService` is not "a type of LoggableMixin" in any meaningful domain sense).
- **Add a single, focused capability** (logging, serialization, validation, etc.).

The naming convention `SomethingMixin` makes the intent clear.

### Why Use Mixins?

Mixins let you attach **plug-and-play behaviour** to any class without modifying its main inheritance hierarchy. Want logging in your `Service` class? Mix in a `LoggableMixin`. Want JSON serialization? Mix in a `JSONSerializableMixin`. This avoids:

- Polluting your main parent class with unrelated methods.
- Duplicating utility code across multiple unrelated classes.

### Method Resolution Order (MRO)

When a class has multiple parents, a natural question arises: **If two parents define a method with the same name, which one does Python use?**

Python resolves this with the **Method Resolution Order (MRO)** — a deterministic search path that Python follows when looking for a method or attribute. The rules:

1. **Start with the current class** (the child).
2. **Search parents from left to right**, in the exact order they appear in the class definition.
3. **Continue up the hierarchy** until reaching the universal base class `object`.
4. Python stops at the **first match** it finds.

**Because of MRO, the order in which you list parent classes matters significantly.**

### Use Case: `LoggableMixin`

```python
class LoggableMixin:
    """Mixin: adds a log() helper to any class."""
    def log(self, message: str):
        print(f"[{self.__class__.__name__}] {message}")
```

```python
class Service:
    def process(self, data: str) -> str:
        return data.upper()


class LoggableService(LoggableMixin, Service):   # ← Mixin listed BEFORE Service
    def process(self, data: str) -> str:
        self.log(f"Processing: {data}")
        result = super().process(data)
        self.log(f"Done: {result}")
        return result
```

**What is the MRO for `LoggableService`?**

```python
print(LoggableService.__mro__)
```

**Output:**

```
(<class 'LoggableService'>, <class 'LoggableMixin'>, <class 'Service'>, <class 'object'>)
```

Python searches in this order: `LoggableService` → `LoggableMixin` → `Service` → `object`.

**Tracing through the method calls:**

```python
svc = LoggableService()
svc.process("hello")
```

1. `svc.process("hello")` → Python finds `LoggableService.process()` (first in MRO).
2. `self.log("Processing: hello")` → Python checks `LoggableService` (no `log`), then `LoggableMixin` (found). Prints `[LoggableService] Processing: hello`.
3. `super().process(data)` → `super()` means "next in MRO after `LoggableService`," which is `LoggableMixin`. But `LoggableMixin` has no `process()`, so Python continues to `Service`. Found → runs `Service.process("hello")` → returns `"HELLO"`.
4. `self.log("Done: HELLO")` → Same as step 2. Prints `[LoggableService] Done: HELLO`.

**Output:**

```
[LoggableService] Processing: hello
[LoggableService] Done: HELLO
```

### Verifying `isinstance()` with Multiple Parents

```python
svc = LoggableService()
print(isinstance(svc, LoggableMixin))   # True
print(isinstance(svc, Service))          # True
print(isinstance(svc, LoggableService))  # True
```

An instance of `LoggableService` is simultaneously a `LoggableService`, a `LoggableMixin`, and a `Service`.

### Why List Mixins First?

The convention `class LoggableService(LoggableMixin, Service)` lists the mixin *before* the main parent class. This is important because:

1. **MRO searches left to right.** If a mixin overrides a method that the main parent also has, listing the mixin first ensures the mixin's version takes priority.
2. **Semantic clarity.** The rightmost class is typically the "primary" parent (the main identity of the class), and the leftmost classes are mixins that add supplementary behaviour.

### What My Professor Didn't Explain

- **Python's MRO uses the C3 linearization algorithm**, not simple left-to-right traversal. For most cases (especially with one main parent + mixins), the result looks like simple left-to-right ordering. But in complex diamond inheritance patterns (where two parents share a common grandparent), C3 linearization ensures each class appears only once in the MRO and the order is consistent. You can always inspect the MRO with `ClassName.__mro__` or `ClassName.mro()` to see the exact order.
- **The diamond problem** occurs when two parent classes inherit from the same grandparent. Python handles this correctly through MRO (calling the shared grandparent's method only once), but other languages (like C++) struggle with it. This is one area where Python's design shines.
- **Mixins should generally not have `__init__` methods.** If they do, you need to be very careful with `super().__init__()` chains and cooperative multiple inheritance, which is an advanced topic. Keep mixins simple — just methods, no state.

### Warnings and Best Practices

- **Keep mixins small and focused.** A mixin should do one thing. If a mixin has many methods or its own attributes, it is probably a full class and should be used via composition instead.
- **Avoid deep multiple inheritance chains.** If a class inherits from 5+ parents, the MRO becomes very difficult to reason about. Prefer composition (storing objects as attributes) for complex combinations.
- **Name your mixins with the `Mixin` suffix.** This makes it immediately clear that the class is designed to be mixed into others, not instantiated on its own.
- **List mixins before the primary parent** in the inheritance list: `class MyClass(MixinA, MixinB, MainParent)`.

---

## 9. Summary Cheat Sheet

| Concept | Syntax | Key Point |
|---|---|---|
| **Inheritance** | `class Sub(Parent):` | Sub receives all parent attributes and methods |
| **Parent constructor** | `super().__init__(...)` | Always call as the first line of `__init__` — without it, parent attributes don't exist |
| **Method override** | Define same method name in subclass | Python picks the most specific (subclass) version first |
| **Extend via `super()`** | `result = super().method()` then add more | Reuse parent logic without duplicating it |
| **Polymorphism** | Same call, different classes | Code works with any subclass uniformly — no type-checking needed |
| **`isinstance()`** | `isinstance(obj, Class)` | Returns `True` for the class and any of its subclasses |
| **Abstract class** | `class Foo(ABC):` | Cannot be instantiated — serves as a strict blueprint |
| **Abstract method** | `@abstractmethod` | Subclasses must implement; may optionally have a body accessible via `super()` |
| **Abstract property** | `@property` then `@abstractmethod` | Subclasses must implement as a `@property`; decorator order matters |
| **Mixin** | Small class adding one focused behaviour | List before the concrete parent class for correct MRO |
| **MRO** | `ClassName.__mro__` | The search path Python follows: child → left parent → right parent → ... → `object` |

---

## Quick Reference: When to Use What

| Situation | Tool |
|---|---|
| Multiple classes share the same attributes/methods | **Inheritance** — put shared code in a parent |
| A child needs the parent's setup + its own | **`super().__init__()`** — call the parent constructor |
| A child needs completely different behaviour for a method | **Method override** — define the same-named method in the child |
| A child needs the parent's method behaviour + extra steps | **Method extension** — call `super().method()` and add to it |
| You want one loop/function to work with many class types | **Polymorphism** — ensure all classes implement the same method |
| You need to confirm an object's type before accessing subclass-specific features | **`isinstance()`** |
| You must enforce that all subclasses implement certain methods | **ABC with `@abstractmethod`** |
| You want to add a focused utility (logging, serialization) to a class | **Mixin** |
