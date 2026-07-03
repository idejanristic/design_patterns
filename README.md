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

* **[01_adapter.md](structurals/01_adapter.md)** – Making incompatible interfaces work together.
* **[02_bridge.md](structurals/02_bridge.md)** – Decoupling an abstraction from its implementation.
* **[03_Composite.md](structurals/03_composite.md)** – Composing objects into tree structures to represent part-whole hierarchies.
* **[04_decorator.md](structurals/04_decorator.md)** – Dynamically adding new behavior to an object.
* **[05_facade.md](structurals/05_facade.md)** – Providing a simplified interface to a complex subsystem.
* **[06_flyweight.md](structurals/06_flyweight.md)** – Sharing common state among multiple objects to reduce memory usage.
* **[07_proxy.md](structurals/07_proxy.md)** – Providing a placeholder or surrogate for another object.

---

### 3. Behavioral Patterns (10 Patterns)

Behavioral patterns focus on **communication between objects** and **how responsibilities are distributed** within a system.

* **[01_chain_of_responsibility.md](behaviorals/01_chain_of_responsibility.md)** – Passing requests through a chain of handlers.
* **[02_command.md](behaviorals/02_command.md)** – Encapsulating requests as objects.
* **[03_iterator.md](behaviorals/03_iterator.md)** – Accessing elements sequentially without exposing the underlying structure.
* **[04_mediator.md](behaviorals/04_mediator.md)** – Centralizing communication between multiple objects.
* **[05_memento.md](behaviorals/05_memento.md)** – Saving and restoring an object's previous state.
* **[06_observer.md](behaviorals/06_observer.md)** – Notifying multiple objects when another object changes.
* **[07_state.md](behaviorals/07_state.md)** – Changing an object's behavior based on its internal state.
* **[08_strategy.md](behaviorals/08_strategy.md)** – Selecting an algorithm at runtime.
* **[09_template_method.md](behaviorals/09_template_method.md)** – Defining the skeleton of an algorithm in a base class while allowing subclasses to customize specific steps.
* **[10_visitor.md](behaviorals/10_visitor.md)** – Adding new operations to existing object structures without modifying them.

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


