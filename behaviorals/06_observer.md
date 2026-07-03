# Observer Design Pattern

## Definition

**Observer** is a **behavioral design pattern** that allows you to define a subscription mechanism so that multiple objects can be automatically notified about any events that happen to the object they are observing.

In other words, one object (**Subject/Publisher**) maintains a list of dependent objects (**Observers/Subscribers**) and automatically notifies them whenever its state changes.

---

## Problem it solves

Assume we have an e-commerce system.

When an order is created:

* send email,
* send SMS,
* update analytics,
* create logs.

Without the Observer pattern:

```php
class OrderService
{
    public function createOrder(): void
    {
        // create order

        $emailService->send();
        $smsService->send();
        $analytics->track();
        $logger->log();
    }
}
```

Problems:

* tight coupling,
* hard to add new actions,
* `OrderService` knows too much,
* violates the Open-Closed Principle.

---

## Idea

Instead of:

```text
OrderService
    ↓
EmailService
    ↓
SmsService
    ↓
Analytics
    ↓
Logger
```

we create:

```text
OrderService (Subject)
            ↓
         notify()
            ↓
--------------------------------
|         |         |          |
Email     SMS    Analytics   Logger
```

`OrderService` does not know what will happen.

It only calls:

```php
$this->notify();
```

---

## Structure

```text
Client
   |
Subject
   |
----------------------------
|            |             |
Observer1  Observer2   Observer3
```

---

## Roles

### Subject (Publisher)

Maintains a list of observers and sends notifications.

---

### Observer (Subscriber)

Defines:

```php
update()
```

---

### Concrete Observer

Implements the reaction to events.

---

### Client

Registers observers.

---

## Example 1 – Order System

### Observer

```php
interface Observer
{
    public function update(
        string $event
    ): void;
}
```

---

### Subject

```php
class OrderService
{
    private array $observers = [];

    public function attach(
        Observer $observer
    ): void {
        $this->observers[] = $observer;
    }

    public function notify(
        string $event
    ): void {
        foreach ($this->observers as $observer) {
            $observer->update($event);
        }
    }

    public function create(): void
    {
        echo "Order created" . PHP_EOL;

        $this->notify('order.created');
    }
}
```

---

### Email Observer

```php
class EmailObserver implements Observer
{
    public function update(
        string $event
    ): void {
        echo "Email sent" . PHP_EOL;
    }
}
```

---

### SMS Observer

```php
class SmsObserver implements Observer
{
    public function update(
        string $event
    ): void {
        echo "SMS sent" . PHP_EOL;
    }
}
```

---

### Analytics Observer

```php
class AnalyticsObserver implements Observer
{
    public function update(
        string $event
    ): void {
        echo "Analytics updated" . PHP_EOL;
    }
}
```

---

### Usage

```php
$order = new OrderService();

$order->attach(new EmailObserver());
$order->attach(new SmsObserver());
$order->attach(new AnalyticsObserver());

$order->create();
```

Output:

```text
Order created
Email sent
SMS sent
Analytics updated
```

---

## What did we gain?

`OrderService` does not know:

```text
EmailService
SmsService
AnalyticsService
```

It only knows:

```php
$this->notify();
```

---

## Visual

```text
OrderService
      ↓
   notify()
      ↓
----------------------
|         |          |
Email     SMS    Analytics
```

---

## Example 2 – YouTube Channel

### Subject

```php
class Channel
{
    private array $subscribers = [];

    public function subscribe(Subscriber $subscriber): void
    {
        $this->subscribers[] = $subscriber;
    }

    public function upload(string $video): void
    {
        echo "Uploaded: {$video}" . PHP_EOL;

        foreach ($this->subscribers as $subscriber) {
            $subscriber->update($video);
        }
    }
}
```

---

### Observer

```php
class Subscriber
{
    public function __construct(
        private string $name
    ) {
    }

    public function update(string $video): void
    {
        echo "{$this->name} notified about {$video}" . PHP_EOL;
    }
}
```

---

### Usage

```php
$channel = new Channel();

$channel->subscribe(new Subscriber('John'));
$channel->subscribe(new Subscriber('Alice'));

$channel->upload('Design Patterns');
```

Output:

```text
Uploaded: Design Patterns
John notified about Design Patterns
Alice notified about Design Patterns
```

---

## Advantages

### 1. Loose Coupling

The subject does not know:

```text
Who is listening?
How many listeners exist?
What they do?
```

---

### 2. Open-Closed Principle (OCP)

Adding:

```php
class SlackObserver
{
}
```

does not require changes in `OrderService`.

---

### 3. Single Responsibility Principle (SRP)

* OrderService → creates orders
* EmailObserver → sends emails
* SmsObserver → sends SMS
* AnalyticsObserver → analytics

---

### 4. Dynamic subscriptions

```php
attach()
detach()
```

at runtime.

---

### 5. Broadcast communication

One event can trigger multiple actions:

```text
Email
SMS
Analytics
Logger
```

---

## Disadvantages

### 1. Unexpected side effects

```php
$order->create();
```

may trigger multiple hidden actions.

---

### 2. Harder debugging

It is not always clear who reacted and in what order.

---

### 3. Performance issues

A large number of observers can slow down execution.

---

### 4. Possible memory leaks

If observers are not removed (`detach()`), they may stay registered longer than needed.

---

## When to use Observer?

* event systems
* GUI applications
* notification systems
* pub/sub systems
* domain events
* event-driven architecture

---

## When to avoid it?

* when there is only one listener
* when side effects must be fully explicit and predictable
* when it introduces too much hidden behavior

---

## Real-world example

### YouTube

The channel emits:

```text
New Video
```

Subscribers are automatically notified.

---

### Newsletter

```text
Newsletter → Subscribers → Email
```

---

## Laravel example

```php
event(new OrderCreated($order));
```

Listeners:

```php
class SendEmailListener
{
    public function handle(OrderCreated $event) {}
}

class UpdateStatisticsListener
{
    public function handle(OrderCreated $event) {}
}
```

---

## Observer vs Mediator

Observer:

```text
Publisher → Many Subscribers
```

Mediator:

```text
Many Components → Mediator
```

---

## Observer vs Chain of Responsibility

Observer:

```text
One event → Many listeners
```

Chain:

```text
A → B → C (chain)
```

---

## Observer vs Command

Observer = events
Command = actions

---

## Interview rule

**Observer = defines a one-to-many dependency so that when one object changes state, all its dependents are automatically notified.**
