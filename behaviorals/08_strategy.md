# Strategy Design Pattern

## Definition

**Strategy** is a **behavioral design pattern** that lets you define a family of algorithms, put each of them into a separate class, and make their objects interchangeable.

In other words, Strategy allows you to change an algorithm’s behavior at runtime by swapping one strategy object for another.

---

## Problem It Solves

Let’s assume we have an e-commerce system with multiple payment methods:

* Credit Card
* PayPal
* Crypto

### Without Strategy Pattern:

```php
class PaymentService
{
    public function pay(float $amount, string $method): void
    {
        if ($method === 'card') {
            echo "Paying {$amount} by card";
        }

        if ($method === 'paypal') {
            echo "Paying {$amount} by PayPal";
        }

        if ($method === 'crypto') {
            echo "Paying {$amount} by crypto";
        }
    }
}
```

### Problems:

* too many `if/else` statements,
* hard to extend with new algorithms,
* violates Open/Closed Principle,
* one class knows too much.

---

## Idea

Instead of:

```text
PaymentService
   ↓
if card
if paypal
if crypto
```

each algorithm becomes a separate class:

```text
PaymentService
      ↓
PaymentStrategy
      ↓
---------------------
|         |         |
Card    PayPal   Crypto
```

`PaymentService` does not know how payment works.

It only calls:

```php
$strategy->pay($amount);
```

---

## Structure

```text
Client
   |
Context
   |
Strategy
   |
-------------------------
|           |           |
StrategyA StrategyB StrategyC
```

---

## Roles

### Strategy

Defines a common interface for all algorithms.

---

### Concrete Strategy

Implements a specific algorithm.

---

### Context

Uses a strategy object.

---

### Client

Chooses which strategy to use.

---

## Example 1 – Payment System

### Strategy

```php
interface PaymentStrategy
{
    public function pay(float $amount): void;
}
```

---

### Credit Card Strategy

```php
class CreditCardPayment implements PaymentStrategy
{
    public function pay(float $amount): void
    {
        echo "Paid {$amount} by credit card";
    }
}
```

---

### PayPal Strategy

```php
class PayPalPayment implements PaymentStrategy
{
    public function pay(float $amount): void
    {
        echo "Paid {$amount} by PayPal";
    }
}
```

---

### Crypto Strategy

```php
class CryptoPayment implements PaymentStrategy
{
    public function pay(float $amount): void
    {
        echo "Paid {$amount} by crypto";
    }
}
```

---

### Context

```php
class PaymentService
{
    public function __construct(
        private PaymentStrategy $strategy
    ) {}

    public function setStrategy(PaymentStrategy $strategy): void
    {
        $this->strategy = $strategy;
    }

    public function pay(float $amount): void
    {
        $this->strategy->pay($amount);
    }
}
```

---

### Usage

```php
$service = new PaymentService(new CreditCardPayment());

$service->pay(100);

$service->setStrategy(new PayPalPayment());

$service->pay(200);
```

Output:

```text
Paid 100 by credit card
Paid 200 by PayPal
```

---

## What Do We Gain?

Without Strategy pattern:

```text
PaymentService
   ↓
if/else logic
```

With Strategy pattern:

```text
PaymentService
   ↓
PaymentStrategy
   ↓
Card / PayPal / Crypto
```

Each algorithm is separated and interchangeable.

---

## Example 2 – Sorting

### Strategy

```php
interface SortStrategy
{
    public function sort(array $items): array;
}
```

---

### Ascending Sort

```php
class AscSort implements SortStrategy
{
    public function sort(array $items): array
    {
        sort($items);
        return $items;
    }
}
```

---

### Descending Sort

```php
class DescSort implements SortStrategy
{
    public function sort(array $items): array
    {
        rsort($items);
        return $items;
    }
}
```

---

### Context

```php
class Sorter
{
    public function __construct(
        private SortStrategy $strategy
    ) {}

    public function sort(array $items): array
    {
        return $this->strategy->sort($items);
    }
}
```

---

### Usage

```php
$sorter = new Sorter(new AscSort());

print_r($sorter->sort([3, 1, 2]));
```

Output:

```text
[1, 2, 3]
```

---

## Advantages

### 1. Eliminates if/else blocks

Instead of:

```text
if card
if paypal
if crypto
```

you get:

```text
CardStrategy
PayPalStrategy
CryptoStrategy
```

---

### 2. Follows Single Responsibility Principle (SRP)

Each class has one responsibility:

* CardStrategy → card payments
* PayPalStrategy → PayPal payments
* CryptoStrategy → crypto payments

---

### 3. Follows Open/Closed Principle (OCP)

You can add new strategies without modifying existing code.

---

### 4. Strategies are interchangeable

```php
$service->setStrategy(new CryptoPayment());
```

---

### 5. Easier testing

You can mock the strategy interface and test the context independently.

---

## Disadvantages

### 1. Many classes

Each new algorithm adds a new class.

---

### 2. Client must choose strategy

The client is responsible for selecting the correct strategy.

---

### 3. Overengineering risk

For very simple logic, Strategy can be unnecessary complexity.

---

## When to Use Strategy

* Payment systems
* Sorting algorithms
* Compression algorithms
* Authentication providers
* Tax calculations
* Shipping cost calculations
* Pricing engines

---

## When NOT to Use It

* Only one algorithm exists
* Logic is extremely simple
* No expected variation in behavior

---

## Real-World Examples

### Google Maps

```text
Driving / Walking / Cycling / Transit
```

Each route uses a different algorithm → Strategy pattern.

---

### Compression Tools

* ZIP
* RAR
* TAR

Different compression algorithms selected at runtime.

---

## Laravel Examples

### Cache Drivers

```php
Cache::store('redis');
Cache::store('file');
Cache::store('database');
```

Each driver is a strategy.

---

### Filesystem Drivers

```php
Storage::disk('local');
Storage::disk('s3');
```

Different storage strategies behind a common interface.

---

## Strategy vs State

### Strategy

* Focus: how an algorithm works
* Chosen by client
* Independent algorithms

Example:

```text
PayPal / Card / Crypto
```

---

### State

* Focus: object behavior based on internal state
* Object changes state itself

Example:

```text
Playing / Paused / Stopped
```

---

## Strategy vs Command

### Strategy

Represents an algorithm.

### Command

Represents an action/request.

---

## Strategy vs Template Method

### Strategy

Uses composition (swap objects).

### Template Method

Uses inheritance (override steps).

---

## Interview Summary

**Strategy Pattern:**

> Defines a family of algorithms, encapsulates each one, and makes them interchangeable.

Key ideas:

```text
Algorithm family
Interchangeable behavior
Composition over inheritance
Runtime switching
Open-Closed Principle
```

Strategy is used to replace conditional logic with interchangeable algorithms that can be selected at runtime.
