# Topic: SOFTWARE DEVELOPMENT PROCESS

## DDD. What is DDD?

DDD stands for Domain-Driven Design. It is a design approach that models software as close as possible with the domain knowledge provided by domain experts.

## 2 base foundations of DDD

2 base foundations of DDD are ubiquitous languages and model-driven design.

- Ubiquitous language is a dictionary that describes things to create a common understanding between team and domain expert. That includes names of all classes, names of all services, rules that software needs to follow...

- Model-driven design is a design approach that focuses on data models. Model-driven design contains a few key components like:

  - Entity: is the thing that is identical and its identity is immutable.
  - Value object: is the thing that we care only about its value and don't care about its identity.
  - Service: is the thing that its behavior does not belong to any entity.
  - Module: domain model is divided into smaller modules to be easier to develop and maintain.
  - Aggregate: is the thing that groups several things into a united block, and this block communicates with the outside world through the aggregate root.
  - Factory: is the thing that creates complicated things.
  - Repository: is the thing that refers to entities and has methods to create, update, delete, read... entities.

## TDD. What is TDD?

TDD (Test Driven Development) is a programming approach where we write the tests first before implementing for every small functionality of an application. The tests would fail since there was no code is written. Then you start writing the code to make tests pass.

It provides some benefits during the development, like ensuring the application is written for testability and improving the test coverage. The tests themself can serve as living documentation on how the functionality is used.

The steps to follow when using TDD:

1. Write the test
2. Make it fail
3. Change the code to make it pass
4. Refactoring and repeat

## What is BDD?

BDD, Behaviour-Driven Development, is a _agile software development process_ that encourages collaboration among developers, quality assurance testers, and customer representatives in a software project. BDD is considered an effective technical practice especially when the "problem space" of the business problem to solve is complex.

Each user story should, in some way, follow the following structure:

```
Title
  An explicit title.
Narrative
  A short introductory section with the following structure:
  As a: the person or role who will benefit from the feature;
  I want: the feature;
  so that: the benefit or value of the feature.
Acceptance criteria
  A description of each specific scenario of the narrative with the following structure:
  Given: the initial context at the beginning of the scenario, in one or more clauses;
  When: the event that triggers the scenario;
  Then: the expected outcome, in one or more clauses.
```

A highly recommended book to read: [BDD in Action: Behavior-driven development for the whole software lifecycle](https://www.amazon.com/BDD-Action-Behavior-driven-development-lifecycle/dp/161729165X) - written by John Smart Ferguson

Below is a comprehensive guide to the most frequently asked design patterns in interviews, organized by category. For each pattern you’ll find:

* **Intent & Motivation**
* **UML/ASCII Diagram**
* **Key Participants**
* **Minimal Code Example**
* **Common Pitfalls & Trade-offs**

At the end, a **“Pattern Comparisons”** section highlights critical differences between often-confused pairs.

---

## 1. Creational Patterns

### 1.1 Singleton

**Intent**: Ensure a class has only one instance and provide a global access point.
**When to Use**: Shared resource (e.g., logging, configuration).

```
+-----------------+
|   Singleton     |
+-----------------+
| -instance       |
+-----------------+
| +getInstance()  |
+-----------------+
```

```python
class SingletonMeta(type):
    _instance = None
    def __call__(cls, *args, **kwargs):
        if cls._instance is None:
            cls._instance = super().__call__(*args, **kwargs)
        return cls._instance

class Logger(metaclass=SingletonMeta):
    def log(self, msg): print(f"[LOG] {msg}")

# Usage
logger1 = Logger()
logger2 = Logger()
assert logger1 is logger2
```

* **Pitfalls**: Hard to test/mocking; hidden dependencies.
* **Alternatives**: Dependency injection.

---

### 1.2 Factory Method vs. Abstract Factory vs. Builder

#### 1.2.1 Factory Method

**Intent**: Define an interface for creating an object, but let subclasses decide which class to instantiate.

```plaintext
Creator
+ factoryMethod(): Product

ConcreteCreatorA
+ factoryMethod(): ConcreteProductA

Product (interface)
ConcreteProductA, ConcreteProductB
```

```java
// Creator
abstract class Dialog {
    abstract Button createButton();
    void render() {
        Button btn = createButton(); btn.onClick(); btn.render();
    }
}
// ConcreteCreator
class WindowsDialog extends Dialog {
    Button createButton() { return new WindowsButton(); }
}
// Product interface
interface Button { void render(); void onClick(); }
// ConcreteProduct
class WindowsButton implements Button { /*...*/ }
```

* **Use when**: You want to defer instantiation to subclasses.
* **Trade-off**: Each new product family requires a new creator subclass.

#### 1.2.2 Abstract Factory

**Intent**: Provide an interface for creating families of related objects without specifying concrete classes.

```plaintext
AbstractFactory
+ createProductA(): AbstractA
+ createProductB(): AbstractB

ConcreteFactory1,2

AbstractA, AbstractB
ConcreteA1, A2, B1, B2
```

* **Use when**: Products belong to families and you want to interchange entire families.
* **Compared to Factory Method**: Factory Method uses one method/subclass; Abstract Factory uses multiple factory methods grouped.

#### 1.2.3 Builder

**Intent**: Separate construction of a complex object from its representation, allowing the same construction process to create different representations.

```plaintext
Director
+ construct(): Product

Builder
+ stepA() + stepB() … + getResult(): Product

ConcreteBuilder
+ implements steps
```

```python
# Product
class Pizza: ...
# Builder interface
class PizzaBuilder:
    def add_cheese(self): pass
    def add_toppings(self): pass
    def build(self): return Pizza()
# ConcreteBuilder
class MargheritaBuilder(PizzaBuilder):
    def __init__(self): self.p = Pizza()
    def add_cheese(self): self.p.cheese = True; return self
    def add_toppings(self): self.p.toppings.append("tomato"); return self
    def build(self): return self.p

# Director
class Cook:
    def make_pizza(self, builder): return builder.add_cheese().add_toppings().build()

pizza = Cook().make_pizza(MargheritaBuilder())
```

* **Use when**: Object has many optional parameters or complex setup steps.
* **Trade-off**: More classes; overhead if object is simple.

---

### 1.3 Prototype

**Intent**: Create new objects by cloning an existing instance.

```python
import copy
class Prototype:
    def __init__(self, state): self.state = state
    def clone(self): return copy.deepcopy(self)

p1 = Prototype({"x":1})
p2 = p1.clone()
```

* **Use when**: Creating object initialization is expensive.

---

## 2. Structural Patterns

### 2.1 Adapter

**Intent**: Convert the interface of a class into another interface clients expect.

```plaintext
Target (expected interface)
Adaptee (incompatible interface)
Adapter implements Target and holds Adaptee
```

```python
class EuropeanSocket:
    def voltage(self): return 230
class USPlug:
    def voltage(self): return 120
class Adapter(EuropeanSocket):
    def __init__(self, us_plug): self.us = us_plug
    def voltage(self): return self.us.voltage()
# Usage
socket = Adapter(USPlug())
print(socket.voltage())  # 120
```

### 2.2 Facade

**Intent**: Provide a unified, simplified interface to a set of interfaces in a subsystem.

```plaintext
Facade
+ operation()
SubsystemA, B, C
```

```python
class CPU: ...
class Memory: ...
class Disk: ...
class Computer:
    def __init__(self):
        self.cpu, self.mem, self.disk = CPU(), Memory(), Disk()
    def start(self):
        self.cpu.freeze()
        self.mem.load(...)
        self.cpu.execute()
```

* **Adapter vs Facade**: Adapter makes two incompatible interfaces work; Facade simplifies a complex subsystem.

### 2.3 Decorator vs Proxy

* **Decorator**: Adds responsibilities to objects dynamically.
* **Proxy**: Controls access to an object (e.g., lazy loading, access control).

```python
class Component: def operation(self): pass
class Concrete(Component): def operation(self): print("doing")
class Decorator(Component):
    def __init__(self, comp): self.comp = comp
    def operation(self):
        print("before")
        self.comp.operation()
        print("after")

# Proxy example: same interface but adds access control
class Proxy(Component):
    def __init__(self, real): self.real = real
    def operation(self):
        if allowed(): self.real.operation()
        else: print("denied")
```

### 2.4 Composite

**Intent**: Compose objects into tree structures to represent part-whole hierarchies.

```plaintext
Component
+ operation()
Leaf, Composite(children[])
```

### 2.5 Bridge

**Intent**: Decouple abstraction from its implementation so they can vary independently.

---

## 3. Behavioral Patterns

### 3.1 Strategy

**Intent**: Define a family of algorithms, encapsulate each one, and make them interchangeable.

```python
class Strategy: def do(self, data): pass
class ConcreteA(Strategy): ...
class Context:
    def __init__(self, strat): self.strat = strat
    def execute(self, data): return self.strat.do(data)
```

* **Strategy vs State**: Strategy delegates to interchangeable algorithms; State changes behavior when internal state changes.

### 3.2 Observer

**Intent**: Define a one-to-many dependency so when one object changes state, all dependents are notified.

```plaintext
Subject + attach()/detach()/notify()
Observers : update()
```

### 3.3 Command

**Intent**: Encapsulate a request as an object, thereby parameterizing clients with queues, requests, and operations.

### 3.4 Template Method

**Intent**: Define skeleton of an algorithm in a method, deferring some steps to subclasses.

### 3.5 Chain of Responsibility, Mediator, Memento, Visitor, Interpreter

*(For brevity, omitted detailed code—be prepared to sketch these in interviews.)*

---

## 4. Key Pattern Comparisons

| Pattern Pair                               | Key Difference                                                                                                                   |
| ------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------- |
| **Factory Method** vs **Builder**          | Factory Method focuses on creating one product per subclass; Builder constructs complex products step-by-step.                   |
| **Factory Method** vs **Abstract Factory** | Factory Method uses inheritance to decide product, Abstract Factory uses object composition to group multiple related factories. |
| **Adapter** vs **Facade**                  | Adapter makes two incompatible interfaces work; Facade provides a simplified interface over many.                                |
| **Decorator** vs **Proxy**                 | Decorator adds behavior; Proxy controls access.                                                                                  |
| **Strategy** vs **State**                  | Strategy chooses algorithm at runtime; State changes behavior when object state changes (often same implementation).             |
| **Observer** vs **Mediator**               | Observer is one-to-many pub/sub; Mediator encapsulates how multiple objects interact (central controller).                       |

---

### References & Further Reading

* *“Design Patterns: Elements of Reusable Object-Oriented Software”* (Gamma et al.)
* *“Head First Design Patterns”* (Freeman & Robson)
* Martin Fowler’s Pattern Catalog: [https://martinfowler.com/εpatterns.html](https://martinfowler.com/εpatterns.html)

