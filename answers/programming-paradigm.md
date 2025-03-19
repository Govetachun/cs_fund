# Topic: PROGRAMMING PARADIGM

## OOP. What is OOP? Why should we use OOP?

OOP stands for object-oriented programming. Like its name, OOP means focusing on objects and building things upon them. In real world, almost everything is an entity with some data and some behaviors, which OOP can be used to articulated quite exactly, compared to other programming paradigms like procedural programming or functional programming.

## What is the 4 principles of OOP?

The four principles of OOP are encapsulation, abstraction, inheritance, and polymorphism.

- Encapsulation is a feature of OOP that wraps things into a unity, like wraps methods and fields into a class, wrapping classes into a package.
- Abstraction means when we use a class, we only care about its public things, like public fields, methods, and don't care about its private stuff, about how it is implemented under the surface.
- Inheritance allows a class to inherit the properties and characteristics of other classes.
- Polymorphism allows using the same type and the same function call for same same but different action of related entities.

## What is composition? Compare composition vs inheritance

Composition is a has-a relationship. Like a house has rooms, a car has wheels. In OOP, the composition relationship is represented as fields in a class.

Inheritance is an is-a relationship. Like a dog is an animal, a rose is a flower. In OOP, the inheritance relationship is represented as a class inheriting another class.

## Interface vs abstract class

The key difference between abstract class and interface is the relationship between it and the classes that extend or implement it.

When class A extends the abstract class B, class A is a B. For example, a cat is an animal. But when class A implements an interface C, it only means A has C characteristics, like the Cat class can implement Climbing Tree interface.

## Explain class constructor. Why does the constructor not return any value?
# Class Constructor

## **What is a Constructor?**
A **constructor** is a special method in a class that is automatically called when an object of the class is created. It initializes the object's attributes and ensures proper setup.

### **Constructor in Different Languages**
- **Python**: `__init__()`
- **C++/Java**: Same name as the class
- **JavaScript**: `constructor()`

### **Example in Python**
```python
class Car:
    def __init__(self, brand, model):
        self.brand = brand
        self.model = model  # Constructor initializes attributes

car = Car("Tesla", "Model S")
print(car.brand)  # Output: Tesla
```
### Why Does a Constructor Not Return a Value?
- Purpose: A constructor's job is to initialize an object, not return a value.
- Implicit Return: It returns a reference to the newly created object, but this is handled internally by the language.
- Special Nature: In Python, __init__() cannot return anything other than None, otherwise it raises an error.

## Construtor vs decorator


| **Feature**      | **Constructor (`__init__`)** | **Decorator (`@decorator`)** |
|-----------------|-----------------------------|------------------------------|
| **Purpose**     | Initializes an object       | Modifies functions or classes |
| **Execution**   | Runs when an object is created | Runs when the decorated function is called |
| **Return Type** | Implicitly returns the object reference | Returns a modified function/class |
| **Scope**       | Used inside a class        | Used for functions and classes |

## What does private, protected, public means and what are the difference?

These keywords indicate the access scope of things like methods, fields in an OOP language.

- Things with "private" scope can be accessed by things in the same class.
- Things with "default" scope can be accessed in the same package.
- Things with "protected" scope is being able to be accessed by things in the same package and outside things that has inheritance relationship.
- Things with "public" scope can be accessed by everything.

## Functional programming. What is FP? Why should we use FP?

# **Functional Programming (FP)**

## **What is Functional Programming?**
Functional Programming (FP) is a **declarative** programming paradigm where computations are treated as **evaluations of pure functions**. It avoids **mutable state** and **side effects**, focusing on **immutability** and **higher-order functions**.

### **Key Concepts of FP**
- **Pure Functions**: No side effects; output depends only on input.
- **Immutability**: Data cannot be changed once created.
- **First-Class Functions**: Functions are treated as values.
- **Higher-Order Functions**: Functions can take other functions as arguments or return them.
- **Referential Transparency**: Expression evaluation always produces the same result.
- **Recursion**: Used instead of loops for iteration.

---

## **Why Should We Use Functional Programming?**

| **Benefit**            | **Explanation** |
|------------------------|----------------|
| **Modularity & Reusability** | Functions are independent and can be reused easily. |
| **Predictability (Pure Functions)** | No side effects, making debugging easier. |
| **Concurrency & Parallelism** | No shared state, avoiding race conditions. |
| **Simpler Debugging & Testing** | Pure functions have deterministic outputs. |
| **Readability & Maintainability** | Declarative style leads to clearer code. |

---

## **Example: Imperative vs Functional Approach**

### **Imperative (Non-FP) Approach**
```python
numbers = [1, 2, 3, 4]
squared = []

for num in numbers:
    squared.append(num ** 2)

print(squared)  # Output: [1, 4, 9, 16]
```

## FP characteristics

Some highlight characteristics of functional programming are:

- Immutability: variable should be set once and not changed.
- Pure function: the return value should depend only on the input value, and create no side effect.
- Function is first-class citizen. We can create a function, assign a function to a variable, pass it as a parameter and return it as a value.

## What is immutability and why is it important?

To be defined.

## What are pure functions and why are they important to functional programming?

To be defined.

## What does it mean when a function is a first-class citizen?

To be defined.

## Lazy evaluation

To be defined.

## What is higher-order functions?

To be defined.

## What are currying and partial application?

To be defined.

## What are pure and impure functions?

To be defined.

## Difference between flatMap() and map()

To be defined.

## Explain functor, applicative, monoid, monad

- Functor is a type class that wraps value in a context and defines a fmap. A fmap is the function that takes another function and a wrapped value, then return a new wrapped value.
- Applicative is a type class that wraps value in a context and defines a function that can take a value in a wrapped context and a function in a wrapped context, apply the function to value, then return the result wrapped in the context.
- Monoid: to be defined.
- Monad is a type class that wraps value in a context and defines a function that can take a value in a wrapped context and a function in a wrapped context, apply the function to value, then return the result wrapped in the context. But the different thing between applicative and monad is the return type of the function. In applicative, the function returns the value directly, while in monad, the function returns the value wrapped in a context.

# **SOLID Principles**

The SOLID principles guide developers in creating software that is easier to maintain, scale, and extend, reducing bugs and ensuring a cleaner, more modular architecture.

## **1. Single Responsibility Principle (SRP)**
**Definition**: A class should have only one reason to change.  
**Key Idea**: Each class should be focused on a single functionality or concern.  
**Benefit**: Simplifies maintenance, improves clarity, and reduces coupling.

---

## **2. Open/Closed Principle (OCP)**
**Definition**: Software entities (classes, modules, functions) should be open for extension but closed for modification.  
**Key Idea**: Instead of modifying existing code, extend it by adding new code.  
**Benefit**: Enhances flexibility and adaptability without altering existing, stable code.

---

## **3. Liskov Substitution Principle (LSP)**
**Definition**: Subtypes must be substitutable for their base types without altering the correctness of the program.  
**Key Idea**: Derived classes should extend the base class’s behavior without changing its meaning.  
**Benefit**: Ensures consistency and prevents unexpected behavior.

---

## **4. Interface Segregation Principle (ISP)**
**Definition**: Clients should not be forced to depend on methods they do not use.  
**Key Idea**: Break down large, unwieldy interfaces into smaller, more specific ones.  
**Benefit**: Reduces the impact of interface changes and simplifies implementation.

---

## **5. Dependency Inversion Principle (DIP)**
**Definition**: High-level modules should not depend on low-level modules. Both should depend on abstractions.  
**Key Idea**: Rely on interfaces or abstract classes rather than concrete implementations.  
**Benefit**: Improves code modularity and makes code easier to test and maintain.