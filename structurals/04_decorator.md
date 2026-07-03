# Decorator Design Pattern

## Definition

**Decorator** is a **structural design pattern** that allows you to dynamically add new behavior or responsibilities to objects by wrapping them inside other objects.

Instead of modifying the original class or creating many subclasses, Decorator extends behavior at runtime using composition.

Simply put:

> "Decorator lets you extend an object’s behavior without changing its code."

---

## Why do we need the Decorator Pattern?

Imagine a simple coffee system:

```php id="c1"
class Coffee
{
    public function getCost(): int
    {
        return 5;
    }
}
```

Now we want variations:

* Milk Coffee
* Sugar Coffee
* Milk + Sugar Coffee
* Milk + Sugar + Cream Coffee

Without Decorator, we would create many classes:

```text id="c2"
Coffee
├── MilkCoffee
├── SugarCoffee
├── MilkSugarCoffee
├── MilkSugarCreamCoffee
└── ...
```

This quickly leads to **class explosion**.

---

## The Idea

Instead of inheritance, we use composition:

```text id="c3"
Coffee
   ↓
MilkDecorator
   ↓
SugarDecorator
```

Each decorator wraps the previous object and adds behavior.

---

## Structure

```text id="c4"
Component
├── ConcreteComponent
└── Decorator
       ↓
   ConcreteDecorator
```

Or more realistically:

```text id="c5"
Component
    ↓
Decorator
    ↓
Decorator
    ↓
ConcreteComponent
```

---

## Roles

* **Component** → common interface
* **ConcreteComponent** → base object
* **Decorator** → wraps a component
* **ConcreteDecorator** → adds behavior

---

## Example 1 – Coffee System

### Component

```php id="e1"
interface Coffee
{
    public function getCost(): int;

    public function getDescription(): string;
}
```

---

### Concrete Component

```php id="e2"
class SimpleCoffee implements Coffee
{
    public function getCost(): int
    {
        return 5;
    }

    public function getDescription(): string
    {
        return 'Coffee';
    }
}
```

---

### Base Decorator

```php id="e3"
abstract class CoffeeDecorator implements Coffee
{
    public function __construct(
        protected Coffee $coffee
    ) {
    }
}
```

---

### Concrete Decorators

#### MilkDecorator

```php id="e4"
class MilkDecorator extends CoffeeDecorator
{
    public function getCost(): int
    {
        return $this->coffee->getCost() + 2;
    }

    public function getDescription(): string
    {
        return $this->coffee->getDescription() . ', milk';
    }
}
```

#### SugarDecorator

```php id="e5"
class SugarDecorator extends CoffeeDecorator
{
    public function getCost(): int
    {
        return $this->coffee->getCost() + 1;
    }

    public function getDescription(): string
    {
        return $this->coffee->getDescription() . ', sugar';
    }
}
```

---

### Usage

```php id="e6"
$coffee = new SimpleCoffee();

$coffee = new MilkDecorator($coffee);
$coffee = new SugarDecorator($coffee);

echo $coffee->getDescription();
echo PHP_EOL;
echo $coffee->getCost();
```

Output:

```text id="e7"
Coffee, milk, sugar
8
```

---

## What happens internally?

```text id="e8"
SugarDecorator
      ↓
MilkDecorator
      ↓
SimpleCoffee
```

Execution flows inward:

```text id="e9"
5 (Coffee)
+ 2 (Milk)
+ 1 (Sugar)
= 8
```

---

## Example 2 – Notification System

### Component

```php id="n1"
interface Notification
{
    public function send(string $message): void;
}
```

---

### Concrete Component

```php id="n2"
class BasicNotification implements Notification
{
    public function send(string $message): void
    {
        echo $message;
    }
}
```

---

### Base Decorator

```php id="n3"
abstract class NotificationDecorator implements Notification
{
    public function __construct(
        protected Notification $notification
    ) {
    }
}
```

---

### Logging Decorator

```php id="n4"
class LoggingDecorator extends NotificationDecorator
{
    public function send(string $message): void
    {
        echo "Logging..." . PHP_EOL;

        $this->notification->send($message);
    }
}
```

---

### Encryption Decorator

```php id="n5"
class EncryptionDecorator extends NotificationDecorator
{
    public function send(string $message): void
    {
        $encrypted = base64_encode($message);

        $this->notification->send($encrypted);
    }
}
```

---

### Usage

```php id="n6"
$notification = new BasicNotification();

$notification = new LoggingDecorator($notification);
$notification = new EncryptionDecorator($notification);

$notification->send('Hello');
```

Output:

```text id="n7"
Logging...
SGVsbG8=
```

---

## Dynamic Composition

Decorators can be combined freely:

```php id="d1"
new LoggingDecorator(
    new EncryptionDecorator(
        new BasicNotification()
    )
);
```

or:

```php id="d2"
new EncryptionDecorator(new BasicNotification());
```

or:

```php id="d3"
new LoggingDecorator(new BasicNotification());
```

No new classes are needed for combinations.

---

## Advantages

### 1. Dynamic behavior extension

Behavior can be added at runtime.

---

### 2. Avoids class explosion

Instead of:

```text id="a1"
MilkCoffee
SugarCoffee
MilkSugarCoffee
...
```

we have reusable decorators.

---

### 3. Open-Closed Principle (OCP)

New behavior is added without modifying existing code.

---

### 4. Single Responsibility Principle (SRP)

Each decorator has one responsibility:

* LoggingDecorator → logging
* EncryptionDecorator → encryption
* MilkDecorator → milk logic

---

### 5. Composition over inheritance

```php id="a2"
new MilkDecorator(new Coffee());
```

instead of:

```php id="a3"
class MilkCoffee extends Coffee {}
```

---

## Disadvantages

### 1. Many small classes

Systems may grow with many decorators.

---

### 2. Harder debugging

Call flow becomes nested:

```text id="a4"
Decorator
  ↓
Decorator
  ↓
Component
```

---

### 3. Order matters

```php id="a5"
new LoggingDecorator(
    new EncryptionDecorator(
        new BasicNotification()
    )
);
```

is not the same as reversing them.

---

### 4. Increased complexity

For simple objects, this can be overkill.

---

## When to use Decorator?

Use it when:

* You need to add behavior dynamically
* You want to avoid modifying existing classes
* You expect many combinations of features
* You want composition instead of inheritance explosion

---

## When to avoid it?

Avoid it when:

* Behavior is fixed and simple
* There are only a few variants
* Extra abstraction adds unnecessary complexity

---

## Real-world analogy

Think of gift wrapping:

```text id="g1"
Book
   ↓
Gift Wrap
   ↓
Ribbon
   ↓
Greeting Card
```

Each layer adds new value without changing the original object.

---

## Decorator vs Adapter

* **Adapter** → changes interface
* **Decorator** → adds behavior

---

## Decorator vs Composite

* **Composite** → tree structure (part-whole)
* **Decorator** → enhances single object behavior

---

## Decorator vs Inheritance

Inheritance creates rigid combinations:

```text id="g2"
Coffee → MilkCoffee → SugarCoffee → MilkSugarCoffee
```

Decorator keeps it flexible:

```text id="g3"
Coffee → MilkDecorator → SugarDecorator
```

---

## Interview Summary

**Decorator = Add responsibilities to an object dynamically without changing its class.**

Key idea:

```php id="g4"
interface Component
{
    public function operation(): void;
}

abstract class Decorator implements Component
{
    public function __construct(
        protected Component $component
    ) {
    }
}
```

Decorator is all about:

> **composition over inheritance + dynamic behavior extension + avoiding class explosion**
