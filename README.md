# Design Patterns Documentation

This repository provides comprehensive documentation for the most important **Design Patterns** used in software engineering. It covers **23 design patterns**, organized into three main categories based on their purpose.

## Project Overview

Design patterns are **reusable solutions to recurring software design problems**. Rather than being complete implementations, they are proven design approaches that help developers build flexible, maintainable, and extensible software.

This project provides a detailed explanation of each pattern, including its purpose, implementation, advantages, disadvantages, and practical examples.

---

##  Project Structure

The project is organized into three major categories.

### 1. Creational Patterns (6 Patterns)

Creational patterns focus on **object creation mechanisms**, making the process more flexible and independent from the concrete classes being instantiated.

* **[01_singleton.md](creationals/01_singleton.md)** – Ensuring that only one instance of a class exists.
* **[02_builder.md](creationals/02_builder.md)** – Constructing complex objects step by step.
* **[03_prototype.md](creationals/03_prototype.md)** – Creating new objects by cloning existing ones.
* **[04_simple_factory.md](creationals/04_simple_factory)** – Creating objects through a simple factory class.
* **[05_factory_method.md](creationals/05_factory_method)** – Defining factory methods that allow subclasses to decide which objects to create.
* **[06_Abstract_Factory.md](creationals/06_abstract_factory.md)** – Creating families of related objects without specifying their concrete classes.

---

### 2. Structural Patterns (7 Patterns)

Structural patterns focus on **how classes and objects are composed** to form larger, more flexible structures.

* **01_Adapter.md** – Making incompatible interfaces work together.
* **02_Bridge.md** – Decoupling an abstraction from its implementation.
* **03_Composite.md** – Composing objects into tree structures to represent part-whole hierarchies.
* **04_Decorator.md** – Dynamically adding new behavior to an object.
* **05_Facade.md** – Providing a simplified interface to a complex subsystem.
* **06_Flyweight.md** – Sharing common state among multiple objects to reduce memory usage.
* **07_Proxy.md** – Providing a placeholder or surrogate for another object.

---

### 3. Behavioral Patterns (10 Patterns)

Behavioral patterns focus on **communication between objects** and **how responsibilities are distributed** within a system.

* **01_Chain_of_Responsibility.md** – Passing requests through a chain of handlers.
* **02_Command.md** – Encapsulating requests as objects.
* **03_Iterator.md** – Accessing elements sequentially without exposing the underlying structure.
* **04_Mediator.md** – Centralizing communication between multiple objects.
* **05_Memento.md** – Saving and restoring an object's previous state.
* **06_Observer.md** – Notifying multiple objects when another object changes.
* **07_State_pattern.md** – Changing an object's behavior based on its internal state.
* **08_Strategy_pattern.md** – Selecting an algorithm at runtime.
* **09_Template_method.md** – Defining the skeleton of an algorithm in a base class while allowing subclasses to customize specific steps.
* **10_Visitor.md** – Adding new operations to existing object structures without modifying them.

---

## When to Use This Repository

This documentation can be used for:

* **Learning** – Understanding the fundamental design patterns used in object-oriented software development.
* **Quick Reference** – Finding the appropriate pattern for a specific design problem.
* **Documentation** – Reviewing the purpose, advantages, disadvantages, and implementation of each pattern.
* **Software Development** – Applying design patterns in real-world projects.

---

## How to Use This Repository

1. Identify the type of design problem you are trying to solve.
2. Browse the corresponding category (Behavioral, Creational, or Structural).
3. Open the relevant Markdown document.
4. Read the explanation, advantages, disadvantages, and implementation examples.
5. Apply the pattern where it best fits your application.

---

## Pattern Categories

| Category       | Focus                                         | Number of Patterns |
| -------------- | --------------------------------------------- | -----------------: |
| **Behavioral** | Communication and interaction between objects |                 10 |
| **Creational** | Object creation and instantiation             |                  6 |
| **Structural** | Composition of classes and objects            |                  7 |

---

## What's Included in Each Pattern

### Each design pattern includes:

* A detailed explanation
* The problem it solves
* Advantages and disadvantages
* Practical code examples
* Common use cases
* Guidelines on when to use (and when not to use) the pattern

---

## Intended Audience

This repository is intended for:

* Software Developers
* Software Architects
* Computer Science Students
* Anyone learning Object-Oriented Design (OOD)
* Developers preparing for technical interviews

---

## About This Documentation

The goal of this project is not only to explain **how** each design pattern works, but also **why** it exists, **when** it should be used, and **what problems** it is designed to solve.

Understanding the intent behind each pattern is just as important as understanding its implementation.

---


