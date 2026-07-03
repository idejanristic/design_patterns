Evo prevoda u istom stilu kao i za **Singleton**, **Builder** i **Prototype**.
# Simple Factory Pattern

> **Note:** **Simple Factory** is **not an official Gang of Four (GoF) design pattern**, but rather a commonly used object creation idiom.

## Definition

**Simple Factory** is a creational pattern that encapsulates object creation in a single class or method, providing a centralized way to create different objects based on input parameters.

In other words, instead of creating objects directly with `new`, clients ask the factory to create the appropriate object for them.

---

Simply put:

> **"Instead of creating objects yourself, let a factory create the right object for you."**

---

## Why do we need Simple Factory?

Without a factory:

```php
$type = 'email';

if ($type === 'email') {
    $notification = new EmailNotification();
} elseif ($type === 'sms') {
    $notification = new SmsNotification();
} elseif ($type === 'push') {
    $notification = new PushNotification();
}
```

Problems:

* lots of `if/else` or `switch` statements,
* object creation logic is scattered throughout the application,
* harder to maintain,
* tight coupling to concrete classes.

---

## The Idea

Instead of:

```php
$notification = new EmailNotification();
```

we write:

```php
$notification = NotificationFactory::make('email');
```

The client doesn't know which concrete class is instantiated.

---

## Structure

```text
Client
   |
   v
Simple Factory
   |
   +----> ConcreteProductA
   |
   +----> ConcreteProductB
   |
   +----> ConcreteProductC
```

---

## Example 1 – Notification System

### Product Interface

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

class PushNotification implements Notification
{
    public function send(string $message): void
    {
        echo "Push: {$message}";
    }
}
```

---

### Simple Factory

```php
class NotificationFactory
{
    public static function make(string $type): Notification
    {
        return match ($type) {
            'email' => new EmailNotification(),
            'sms' => new SmsNotification(),
            'push' => new PushNotification(),
            default => throw new InvalidArgumentException(
                'Unknown notification type.'
            ),
        };
    }
}
```

---

### Usage

```php
$notification = NotificationFactory::make('email');

$notification->send('Hello');
```

Output:

```text
Email: Hello
```

---

## Example 2 – Payment System

### Product Interface

```php
interface PaymentMethod
{
    public function pay(float $amount): void;
}
```

---

### Concrete Products

```php
class CreditCardPayment implements PaymentMethod
{
    public function pay(float $amount): void
    {
        echo "Paid {$amount} by credit card";
    }
}

class PayPalPayment implements PaymentMethod
{
    public function pay(float $amount): void
    {
        echo "Paid {$amount} via PayPal";
    }
}
```

---

### Factory

```php
class PaymentFactory
{
    public static function make(string $method): PaymentMethod
    {
        return match ($method) {
            'card' => new CreditCardPayment(),
            'paypal' => new PayPalPayment(),
            default => throw new InvalidArgumentException(
                'Unknown payment method.'
            ),
        };
    }
}
```

---

### Usage

```php
$payment = PaymentFactory::make('paypal');

$payment->pay(100);
```

---

## Why is this better?

Instead of:

```php
$payment = new PayPalPayment();
```

we write:

```php
$payment = PaymentFactory::make('paypal');
```

The client only knows about:

```php
PaymentMethod
```

and doesn't need to know about:

```php
PayPalPayment
CreditCardPayment
```

---

## Advantages

### 1. Centralized object creation

Instead of:

```php
new EmailNotification();
new SmsNotification();
new PushNotification();
```

throughout the application, we have:

```php
NotificationFactory::make()
```

in one place.

---

### 2. Reduces code duplication

Without a factory:

```php
if (...) {
    new EmailNotification();
}
```

is repeated across the application.

With a factory:

```php
NotificationFactory::make('email');
```

the creation logic exists only once.

---

### 3. Hides implementation details

The client works with:

```php
Notification
```

instead of:

```php
EmailNotification
SmsNotification
PushNotification
```

---

### 4. Easier to change creation logic

Later we can write:

```php
return new EmailNotification(
    $mailer,
    $templateEngine,
    $logger
);
```

without changing the client code.

---

### 5. Easy to understand

For small projects, Simple Factory is often much easier than Factory Method.

---

## Disadvantages

### 1. Violates the Open-Closed Principle (OCP)

Adding:

```php
class SlackNotification implements Notification
{
}
```

requires modifying the factory:

```php
return match ($type) {
    'email' => ...,
    'sms' => ...,
    'push' => ...,
    'slack' => new SlackNotification(),
};
```

Existing code must be changed.

---

### 2. The factory can become large

Over time:

```text
NotificationFactory
├── email
├── sms
├── push
├── slack
├── teams
├── discord
├── webhook
└── ...
```

may grow into a huge `switch` or `match`.

---

### 3. Still depends on concrete classes

The factory knows about:

```php
new EmailNotification()
new SmsNotification()
new PushNotification()
```

so it is still coupled to concrete implementations.

---

### 4. Less flexible than Factory Method

With Factory Method:

```text
Creator
├── EmailFactory
├── SmsFactory
└── PushFactory
```

new factories can be added without modifying existing ones.

With Simple Factory, the existing factory must be modified whenever a new product is introduced.

---

## When should you use Simple Factory?

Use Simple Factory when:

* the project is small or medium-sized,
* there are only a few product types,
* you want centralized object creation,
* the creation logic is relatively simple.

---

## When should you avoid it?

Avoid Simple Factory when:

* there are many product types,
* new products are added frequently,
* you need to fully comply with the Open-Closed Principle,
* the product hierarchy is complex.

---

## Difference between Simple Factory and Factory Method

| Feature               | Simple Factory              | Factory Method    |
| --------------------- | --------------------------- | ----------------- |
| GoF Pattern           | ❌ No                        | ✅ Yes             |
| Number of factories   | One                         | Multiple          |
| Adding a new product  | Modify the existing factory | Add a new factory |
| Open-Closed Principle | ❌ Weaker                    | ✅ Better          |
| Complexity            | Low                         | Higher            |
| Flexibility           | Lower                       | Higher            |

---

## Real-world Example

Imagine a restaurant.

### Simple Factory

```php
BurgerFactory::make('cheeseburger');
BurgerFactory::make('veggie');
BurgerFactory::make('double');
```

One kitchen prepares every type of burger.

---

### Factory Method

```text
BurgerFactory
├── CheeseburgerFactory
├── VeggieBurgerFactory
└── DoubleBurgerFactory
```

Each burger type has its own factory.

---

## Interview Rule

**Simple Factory = One factory creates different objects based on input parameters.**

The key idea:

```php
$product = ProductFactory::make($type);
```

Instead of:

```php
$product = new ConcreteProduct();
```

Simple Factory centralizes object creation and reduces duplicated creation logic, but it is not fully open for extension because adding new products requires modifying the factory itself.
