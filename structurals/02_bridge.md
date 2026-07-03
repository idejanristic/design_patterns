# The Bridge Design Pattern

## Definition

The **Bridge** is a **structural design pattern** that **decouples an abstraction from its implementation**, allowing the two to **vary independently**.

In other words, the Bridge pattern separates:

* **what an object does (the abstraction)** from
* **how it does it (the implementation)**

---

## Why Do We Need the Bridge Pattern?

Suppose we have:

* `Circle`
* `Square`

and:

* `Red`
* `Blue`

Without the Bridge pattern, we might create a separate class for every combination:

```text
Shape
├── RedCircle
├── BlueCircle
├── RedSquare
└── BlueSquare
```

Now suppose we add another color:

* `Green`

Our hierarchy becomes:

```text
Shape
├── RedCircle
├── BlueCircle
├── GreenCircle
├── RedSquare
├── BlueSquare
└── GreenSquare
```

If we later add another shape:

* `Triangle`

the number of classes grows rapidly:

```text
Shapes × Colors
```

This is known as the **Class Explosion Problem**.

---

## The Idea Behind the Bridge Pattern

Instead of creating subclasses for every possible combination:

```text
RedCircle
BlueCircle
GreenCircle
```

we separate the two independent dimensions:

```text
Shape ---------> Color
```

This gives us two independent class hierarchies:

```text
Abstraction
├── Circle
└── Square

Implementation
├── Red
├── Blue
└── Green
```

Each `Shape` is composed with a `Color`.

---

## Structure

```text
Abstraction
    |
RefinedAbstraction
    |
Implementation
    |
ConcreteImplementationA
ConcreteImplementationB
```

Or more simply:

```text
Shape ---------> Color
```

The Bridge pattern favors **composition over inheritance**.

---

## Example 1 – Shapes and Colors

### Implementor

```php
interface Color
{
    public function apply(): string;
}
```

---

### Concrete Implementors

```php
class Red implements Color
{
    public function apply(): string
    {
        return 'red';
    }
}

class Blue implements Color
{
    public function apply(): string
    {
        return 'blue';
    }
}
```

---

### Abstraction

```php
abstract class Shape
{
    public function __construct(
        protected Color $color
    ) {
    }

    abstract public function draw(): void;
}
```

---

### Refined Abstractions

```php
class Circle extends Shape
{
    public function draw(): void
    {
        echo 'Drawing a '
            . $this->color->apply()
            . ' circle';
    }
}

class Square extends Shape
{
    public function draw(): void
    {
        echo 'Drawing a '
            . $this->color->apply()
            . ' square';
    }
}
```

---

### Usage

```php
$shape = new Circle(new Red());
$shape->draw();

echo PHP_EOL;

$shape = new Square(new Blue());
$shape->draw();
```

Output:

```text
Drawing a red circle
Drawing a blue square
```

---

### What Have We Gained?

We can now create:

```php
new Circle(new Red());
new Circle(new Blue());

new Square(new Red());
new Square(new Blue());
```

without creating classes such as:

```text
RedCircle
BlueCircle
RedSquare
BlueSquare
```

The number of classes grows **linearly**, not exponentially.

---

## Example 2 – Notification System

Suppose we have:

### Notification Types

* Alert
* Reminder

and:

### Delivery Channels

* Email
* SMS

Without the Bridge pattern:

```text
EmailAlert
SmsAlert
EmailReminder
SmsReminder
```

With the Bridge pattern:

---

### Implementor

```php
interface Sender
{
    public function send(string $message): void;
}
```

---

### Concrete Implementors

```php
class EmailSender implements Sender
{
    public function send(string $message): void
    {
        echo "Email: {$message}";
    }
}

class SmsSender implements Sender
{
    public function send(string $message): void
    {
        echo "SMS: {$message}";
    }
}
```

---

### Abstraction

```php
abstract class Notification
{
    public function __construct(
        protected Sender $sender
    ) {
    }

    abstract public function notify(string $message): void;
}
```

---

### Refined Abstractions

```php
class Alert extends Notification
{
    public function notify(string $message): void
    {
        $this->sender->send(
            "ALERT: {$message}"
        );
    }
}

class Reminder extends Notification
{
    public function notify(string $message): void
    {
        $this->sender->send(
            "REMINDER: {$message}"
        );
    }
}
```

---

### Usage

```php
$notification = new Alert(
    new EmailSender()
);

$notification->notify('Server is down');

echo PHP_EOL;

$notification = new Reminder(
    new SmsSender()
);

$notification->notify('Meeting at 10 AM');
```

Output:

```text
Email: ALERT: Server is down
SMS: REMINDER: Meeting at 10 AM
```

---

## Advantages

### 1. Prevents Class Explosion

Without the Bridge pattern:

```text
Shapes × Colors
Notifications × Senders
Reports × ExportFormats
```

With the Bridge pattern:

```text
Abstraction
      |
Implementation
```

The number of classes grows linearly instead of exponentially.

---

### 2. Abstraction and Implementation Can Evolve Independently

We can add:

```php
class Green implements Color
{
}
```

without modifying:

```php
Circle
Square
```

Or we can add:

```php
class Triangle extends Shape
{
}
```

without modifying:

```php
Red
Blue
Green
```

---

### 3. Supports the Open-Closed Principle (OCP)

New implementations can be added without changing existing code.

For example:

```text
New Shape
New Color
New Sender
```

---

### 4. Supports the Single Responsibility Principle (SRP)

`Shape` is responsible for:

```text
What is being drawn
```

`Color` is responsible for:

```text
How it is drawn
```

Each class has a single responsibility.

---

### 5. Favors Composition over Inheritance

Instead of:

```php
class RedCircle extends Circle
{
}
```

we use composition:

```php
class Circle
{
    public function __construct(
        private Color $color
    ) {
    }
}
```

This provides greater flexibility.

---

## Disadvantages

### 1. More Classes and Interfaces

For small applications:

```php
new Button();
```

using the Bridge pattern may introduce unnecessary complexity.

---

### 2. Harder to Understand

Developers need to understand two separate hierarchies:

```text
Abstraction
Implementation
```

and how they collaborate.

---

### 3. Additional Level of Indirection

A method call flows through multiple objects:

```text
Client
  ↓
Shape
  ↓
Color
```

This can make execution flow slightly harder to follow.

---

## When Should You Use the Bridge Pattern?

Use the Bridge pattern when:

* ✅ You have two or more independent dimensions of change.
* ✅ You want to avoid class explosion.
* ✅ You want to switch implementations at runtime.
* ✅ You prefer composition over deep inheritance hierarchies.

---

## When Should You Avoid It?

Avoid the Bridge pattern when:

* ❌ There is only one dimension of change.
* ❌ The application is small and simple.
* ❌ The additional abstractions only make the design more complicated.

---

## Real-World Example

Imagine a remote control.

We have:

### Devices

* TV
* Radio

and:

### Remote Controls

* Basic Remote
* Advanced Remote

Without the Bridge pattern:

```text
BasicTvRemote
AdvancedTvRemote
BasicRadioRemote
AdvancedRadioRemote
```

With the Bridge pattern:

```text
Remote ---------> Device
```

```text
Remote
├── BasicRemote
└── AdvancedRemote

Device
├── Tv
└── Radio
```

Any remote can operate any device.

This is one of the most well-known examples of the Bridge pattern.

---

## Bridge vs Adapter

### Adapter

Purpose:

```text
Incompatible Interface A
            ↓
        Adapter
            ↓
Expected Interface B
```

The Adapter pattern allows existing classes with incompatible interfaces to work together.

---

### Bridge

Purpose:

```text
Abstraction
      ↓
Implementation
```

The Bridge pattern separates two independent hierarchies so they can evolve independently.

---

## Bridge vs Strategy

### Bridge

Focus:

```text
Abstraction
      ↓
Implementation
```

It is an architectural pattern used to separate two dimensions of a system.

---

### Strategy

Focus:

```text
Context
     ↓
Algorithm
```

It is used to change an algorithm or behavior at runtime.

---

## Interview Rule

> **Bridge = Decouple an abstraction from its implementation so that the two can vary independently.**

The core idea is:

```php
abstract class Abstraction
{
    public function __construct(
        protected Implementor $implementor
    ) {
    }
}
```

The Bridge pattern embraces:

```text
Composition over Inheritance
```

and solves the:

```text
Class Explosion Problem
```

It is most commonly used when a system has **two independent dimensions of change**—such as **Shapes × Colors**, **Notifications × Senders**, or **Remotes × Devices**—that should be extended and maintained independently.
