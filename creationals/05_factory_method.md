# Factory Method Pattern

## Definition

**Factory Method** is a **creational design pattern** that defines an interface for creating objects, but lets subclasses decide which concrete class to instantiate.

In other words, the Factory Method delegates the object creation process to subclasses.

---

## What does that mean?

The **Factory Method** pattern separates the **creation of an object** from its **usage**.

Instead of creating objects directly with `new`, the client asks a **factory** (creator) to provide the required object.

The base (abstract) factory defines *how* objects should be created, while each concrete factory decides *which* concrete object to create.

Simply put:

> "Don't instantiate objects directly. Let a specialized factory decide which implementation should be created."

---

## Why do we need Factory Method?

Imagine an application that supports multiple notification types:

* Email
* SMS
* Push

Without Factory Method, the client would have to decide which object to instantiate:

```php
$type = 'email';

if ($type === 'email') {
    $notification = new EmailNotification();
} elseif ($type === 'sms') {
    $notification = new SmsNotification();
}
```

Problems:

* many `if/else` or `switch` statements
* tight coupling to concrete classes
* difficult to extend
* violates the Open-Closed Principle (OCP)

---

## The idea

Instead of writing:

```php
$product = new ConcreteProduct();
```

we write:

```php
$product = $this->createProduct();
```

The client doesn't know which concrete class is created.

Each subclass decides what to instantiate.

---

## How Factory Method works

The pattern usually consists of four parts.

### 1. Product

Defines the common interface for all products.

Example:

```php
interface Vehicle
{
    public function drive(): void;
}
```

---

### 2. Concrete Product

Implements the `Product` interface.

Example:

```php
class Car implements Vehicle
{
}

class Bike implements Vehicle
{
}
```

---

### 3. Creator

Declares the factory method.

Example:

```php
abstract class VehicleFactory
{
    abstract public function createVehicle(): Vehicle;
}
```

---

### 4. Concrete Creator

Implements the factory method and creates a specific product.

Example:

```php
class CarFactory extends VehicleFactory
{
    public function createVehicle(): Vehicle
    {
        return new Car();
    }
}
```

---

## Structure

```text
                 Product
                    ▲
                    │
        ┌───────────┴───────────┐
        │                       │
ConcreteProductA         ConcreteProductB


                 Creator
                    │
         factoryMethod()
                    ▲
                    │
        ┌───────────┴───────────┐
        │                       │
ConcreteCreatorA         ConcreteCreatorB
```

The client works only with the `Creator` and the `Product`.

---

## Example 1 – Vehicle Factory

### Product

```php
interface Vehicle
{
    public function drive(): void;
}
```

---

### Concrete Products

```php
class Car implements Vehicle
{
    public function drive(): void
    {
        echo 'Driving a car';
    }
}

class Bike implements Vehicle
{
    public function drive(): void
    {
        echo 'Riding a bike';
    }
}
```

---

### Creator

```php
abstract class VehicleFactory
{
    abstract public function createVehicle(): Vehicle;

    public function deliver(): void
    {
        $vehicle = $this->createVehicle();

        $vehicle->drive();
    }
}
```

---

### Concrete Creators

```php
class CarFactory extends VehicleFactory
{
    public function createVehicle(): Vehicle
    {
        return new Car();
    }
}

class BikeFactory extends VehicleFactory
{
    public function createVehicle(): Vehicle
    {
        return new Bike();
    }
}
```

---

### Usage

```php
$factory = new CarFactory();
$factory->deliver();

$factory = new BikeFactory();
$factory->deliver();
```

Output:

```text
Driving a car
Riding a bike
```

---

## Why is this better?

The client writes:

```php
$factory->deliver();
```

instead of:

```php
new Car();
new Bike();
```

The client depends only on:

```php
Vehicle
```

and

```php
VehicleFactory
```

instead of concrete implementations.

---

## Example 2 – Notification System

### Product

```php
interface Notification
{
    public function send(string $message): void;
}
```

---

### Concrete Products

```php
class EmailNotification implements Notification
{
    public function send(string $message): void
    {
        echo "Email: {$message}";
    }
}

class SmsNotification implements Notification
{
    public function send(string $message): void
    {
        echo "SMS: {$message}";
    }
}
```

---

### Creator

```php
abstract class NotificationFactory
{
    abstract public function createNotification(): Notification;

    public function notify(string $message): void
    {
        $notification = $this->createNotification();

        $notification->send($message);
    }
}
```

---

### Concrete Creators

```php
class EmailFactory extends NotificationFactory
{
    public function createNotification(): Notification
    {
        return new EmailNotification();
    }
}

class SmsFactory extends NotificationFactory
{
    public function createNotification(): Notification
    {
        return new SmsNotification();
    }
}
```

---

### Usage

```php
$factory = new EmailFactory();
$factory->notify('Hello');

$factory = new SmsFactory();
$factory->notify('Hello');
```

Output:

```text
Email: Hello
SMS: Hello
```

---

## Advantages

### 1. Loose Coupling

Instead of:

```php
new EmailNotification();
```

the code uses:

```php
$this->createNotification();
```

The client depends on abstractions rather than concrete classes.

---

### 2. Supports the Open-Closed Principle (OCP)

Adding:

```php
class PushNotification implements Notification
{
}
```

and

```php
class PushFactory extends NotificationFactory
{
}
```

does not require modifying existing factories.

---

### 3. Centralizes Object Creation

Object creation is encapsulated inside:

```php
createNotification()
```

instead of being scattered throughout the application.

---

### 4. Easier Testing

We can introduce a fake implementation:

```php
class FakeNotification implements Notification
{
}
```

without changing the rest of the system.

---

### 5. Supports the Dependency Inversion Principle (DIP)

High-level code depends on:

```php
Notification
```

instead of:

```php
EmailNotification
SmsNotification
```

---

## Disadvantages

### 1. More Classes

Each new product usually requires:

* a concrete product
* a concrete factory

This increases the number of classes.

---

### 2. Increased Complexity

For simple applications:

```php
$mail = new EmailNotification();
```

may be much easier than introducing factories.

---

### 3. Additional Abstraction Layer

Developers need to understand:

* Product
* Creator
* Factory Method
* Concrete Product
* Concrete Creator

which can make the design more difficult for beginners.

---

## When should you use Factory Method?

Use it when:

* a class shouldn't know which concrete object it must create
* you want to separate object creation from business logic
* new product types are expected in the future
* you want to program against interfaces rather than implementations

---

## When should you avoid it?

Avoid it when:

* there is only one implementation
* object creation is trivial

```php
$user = new User();
```

* the additional abstraction only adds unnecessary complexity

---

## Real-world example

Imagine a payment application:

```text
Payment
├── CreditCardPayment
├── PayPalPayment
└── CryptoPayment
```

Each payment method has its own factory:

```text
PaymentFactory
├── CreditCardFactory
├── PayPalFactory
└── CryptoFactory
```

The application works only with:

```php
Payment
```

and doesn't care which concrete implementation is created.

---

## Difference between Simple Factory and Factory Method

### Simple Factory

```php
class NotificationFactory
{
    public static function make(string $type): Notification
    {
        return match ($type) {
            'email' => new EmailNotification(),
            'sms' => new SmsNotification(),
        };
    }
}
```

One factory creates all products based on input parameters.

---

### Factory Method

```php
abstract class NotificationFactory
{
    abstract public function createNotification(): Notification;
}
```

Each subclass decides which product to create.

---

## Difference from Abstract Factory

**Factory Method**

* creates **one product**
* each concrete factory creates a single type of product

```text
CarFactory → Car
BikeFactory → Bike
```

**Abstract Factory**

* creates **a family of related products**
* one factory creates multiple compatible objects

```text
WindowsFactory
├── WindowsButton
└── WindowsCheckbox
```

---

## Interview Rule

> **Factory Method = Define an interface for creating objects, but let subclasses decide which concrete class to instantiate.**

The key idea is:

```php
abstract class Creator
{
    abstract public function factoryMethod(): Product;
}
```

The client works only with:

```php
Product
Creator
```

and knows nothing about:

```php
ConcreteProductA
ConcreteProductB
```

As a result, Factory Method reduces coupling between the client and concrete classes, makes the system easier to extend, and aligns well with the **SOLID principles**, especially the **Open-Closed Principle (OCP)** and the **Dependency Inversion Principle (DIP)**.
