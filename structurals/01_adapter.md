# Adapter Design Pattern

## Definition

**Adapter** is a **structural design pattern** that allows objects with incompatible interfaces to work together.

Instead of modifying existing classes, the Adapter wraps an existing object and translates its interface into one that the client expects.

Simply put:

> "Adapter acts as a translator between two incompatible interfaces."

---

## Why do we need the Adapter Pattern?

Imagine our application expects the following interface:

```php
interface PaymentGateway
{
    public function pay(float $amount): void;
}
```

However, we're using a third-party library:

```php
class StripeApi
{
    public function makePayment(float $amount): void
    {
        echo "Stripe payment: {$amount}";
    }
}
```

Trying to use it directly:

```php
$gateway = new StripeApi();

$gateway->pay(100);
```

Results in:

```text
Call to undefined method StripeApi::pay()
```

The application expects:

```text
pay()
```

but the library provides:

```text
makePayment()
```

The interfaces are incompatible.

---

## The Idea

Instead of changing the third-party library, we create an Adapter.

```text
Client
   |
PaymentGateway
   |
StripeAdapter
   |
StripeApi
```

The Adapter translates:

```text
pay()  --->  makePayment()
```

The client continues using the expected interface without knowing anything about the underlying implementation.

---

## How does the Adapter Pattern work?

The Adapter pattern usually consists of four parts.

### 1. Client

The code that wants to use an object.

---

### 2. Target

The interface that the client expects.

```php
interface PaymentGateway
{
    public function pay(float $amount): void;
}
```

---

### 3. Adaptee

An existing class with an incompatible interface.

```php
class StripeApi
{
    public function makePayment(float $amount): void
    {
        echo "Stripe payment: {$amount}";
    }
}
```

---

### 4. Adapter

Implements the expected interface while internally delegating calls to the adaptee.

```php
class StripeAdapter implements PaymentGateway
{
    public function __construct(
        private StripeApi $stripe
    ) {
    }

    public function pay(float $amount): void
    {
        $this->stripe->makePayment($amount);
    }
}
```

---

## Structure

```text
Client
   |
Target Interface
   |
Adapter
   |
Adaptee
```

Where:

* **Client** uses the object.
* **Target** is the interface the client expects.
* **Adaptee** is the existing class with an incompatible interface.
* **Adapter** converts one interface into another.

---

## Example 1 – Payment Gateway

### Target

```php
interface PaymentGateway
{
    public function pay(float $amount): void;
}
```

---

### Adaptee

```php
class StripeApi
{
    public function makePayment(float $amount): void
    {
        echo "Stripe payment: {$amount}";
    }
}
```

---

### Adapter

```php
class StripeAdapter implements PaymentGateway
{
    public function __construct(
        private StripeApi $stripe
    ) {
    }

    public function pay(float $amount): void
    {
        $this->stripe->makePayment($amount);
    }
}
```

---

### Usage

```php
$stripe = new StripeApi();

$gateway = new StripeAdapter($stripe);

$gateway->pay(100);
```

Output:

```text
Stripe payment: 100
```

The client doesn't know it's using `StripeApi`.

It only knows about:

```php
PaymentGateway
```

---

## Example 2 – Notification Service

Suppose our application expects:

```php
interface Notification
{
    public function send(string $message): void;
}
```

But a third-party SDK provides:

```php
class SlackSdk
{
    public function postMessage(string $text): void
    {
        echo "Slack: {$text}";
    }
}
```

---

### Adapter

```php
class SlackAdapter implements Notification
{
    public function __construct(
        private SlackSdk $slack
    ) {
    }

    public function send(string $message): void
    {
        $this->slack->postMessage($message);
    }
}
```

---

### Usage

```php
$notification = new SlackAdapter(
    new SlackSdk()
);

$notification->send('Server started');
```

Output:

```text
Slack: Server started
```

---

## Adapter with Data Transformation

An Adapter doesn't have to translate only method names.

It can also transform data.

```php
class LegacyTemperatureApi
{
    public function getTemperature(): float
    {
        return 86;
    }
}
```

The API returns temperatures in Fahrenheit.

Our application expects Celsius.

---

### Adapter

```php
interface TemperatureService
{
    public function getTemperature(): float;
}

class TemperatureAdapter implements TemperatureService
{
    public function __construct(
        private LegacyTemperatureApi $api
    ) {
    }

    public function getTemperature(): float
    {
        $fahrenheit = $this->api->getTemperature();

        return ($fahrenheit - 32) * 5 / 9;
    }
}
```

---

## What did we gain?

Instead of changing the third-party library:

```php
StripeApi
```

we simply wrapped it:

```text
Client
      |
PaymentGateway
      |
StripeAdapter
      |
StripeApi
```

The rest of the application remains unchanged.

---

## Advantages

### 1. Allows incompatible classes to work together

```text
Application
     |
Adapter
     |
Third-party library
```

No changes are required to existing code.

---

### 2. Follows the Single Responsibility Principle (SRP)

The original class:

```php
StripeApi
```

remains unchanged.

All adaptation logic lives inside:

```php
StripeAdapter
```

---

### 3. Follows the Open-Closed Principle (OCP)

Adding another provider:

```text
PayPalApi
```

only requires creating:

```text
PayPalAdapter
```

without modifying existing code.

---

### 4. Reduces coupling

The client depends on:

```php
PaymentGateway
```

instead of:

```php
StripeApi
PayPalApi
```

---

### 5. Makes implementations easy to replace

```php
$gateway = new StripeAdapter(
    new StripeApi()
);
```

can later become:

```php
$gateway = new PayPalAdapter(
    new PayPalApi()
);
```

without changing the rest of the application.

---

## Disadvantages

### 1. Increases the number of classes

For each external library, we often introduce:

```text
Interface
Adapter
Third-party class
```

The number of classes grows.

---

### 2. Adds another layer of abstraction

The call flow becomes:

```text
Client
  ↓
Adapter
  ↓
Adaptee
```

which can make execution flow slightly harder to follow.

---

### 3. Can hide poor architecture

If a system contains many layers of adapters:

```text
Adapter
  ↓
Adapter
  ↓
Adapter
```

it may indicate that the architecture needs improvement.

---

### 4. Small performance overhead

Instead of:

```php
$api->makePayment();
```

we call:

```php
$adapter->pay();
```

This additional level of indirection is usually negligible.

---

## When should you use the Adapter Pattern?

Use it when:

* integrating third-party libraries
* working with legacy code
* migrating from one API to another
* standardizing different implementations behind a common interface

---

## When should you avoid it?

Avoid it when:

* you control both interfaces and can modify them directly
* there are no incompatible interfaces
* the additional abstraction only makes the design more complicated

---

## Real-world example

Think about a travel power adapter.

```text
EU Wall Socket
       |
Power Adapter
       |
US Charger
```

A US charger cannot plug directly into a European outlet.

The adapter translates:

```text
EU socket interface
        ↓
Power adapter
        ↓
US plug interface
```

The exact same idea applies in software.

---

## Adapter vs Facade

### Adapter

Purpose:

```text
Incompatible Interface A
            ↓
        Adapter
            ↓
Expected Interface B
```

Converts one interface into another.

---

### Facade

Purpose:

```text
Subsystem A
Subsystem B
Subsystem C
        ↓
      Facade
```

Provides a simplified interface to a complex subsystem.

---

## Adapter vs Decorator

### Adapter

Changes the interface.

```text
makePayment()
      ↓
Adapter
      ↓
pay()
```

---

### Decorator

Keeps the same interface but adds behavior.

```text
send()
   ↓
LoggingDecorator
   ↓
CachingDecorator
   ↓
send()
```

---

## Interview Rule

**Adapter = Convert the interface of a class into another interface that clients expect.**

The key idea is:

```php
class Adapter implements Target
{
    public function __construct(
        private Adaptee $adaptee
    ) {
    }

    public function request(): void
    {
        $this->adaptee->specificRequest();
    }
}
```

The Adapter does **not** add new functionality and does **not** create new objects.

Its only responsibility is:

```text
Translate one interface into another.
```

That's why the Adapter pattern is commonly used when integrating **third-party libraries**, **legacy systems**, and **different APIs** that expose **incompatible interfaces**.
