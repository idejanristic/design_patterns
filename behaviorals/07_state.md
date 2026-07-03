# State Design Pattern

## Definition

**State** is a **behavioral design pattern** that allows an object to change its behavior when its internal state changes. It appears as if the object changed its class.

In other words, the State pattern lets an object behave differently depending on its current state, without using large conditional statements like `if/else` or `switch`.

---

## Problem It Solves

Let’s say we are building a media player.

The player can be in one of these states:

```text
Stopped
Playing
Paused
```

### Without State Pattern:

```php
class MediaPlayer
{
    private string $state = 'stopped';

    public function play(): void
    {
        if ($this->state === 'stopped') {
            echo 'Start playing';
            $this->state = 'playing';
        } elseif ($this->state === 'paused') {
            echo 'Resume playing';
            $this->state = 'playing';
        }
    }

    public function pause(): void
    {
        if ($this->state === 'playing') {
            echo 'Paused';
            $this->state = 'paused';
        }
    }

    public function stop(): void
    {
        if (
            $this->state === 'playing' ||
            $this->state === 'paused'
        ) {
            echo 'Stopped';
            $this->state = 'stopped';
        }
    }
}
```

### Problems:

* too many `if/else` statements,
* all state logic is inside one class,
* hard to add new states,
* violates the Open/Closed Principle.

---

## Idea

Instead of:

```text
MediaPlayer
   ↓
if/else logic everywhere
```

each state becomes a separate class:

```text
MediaPlayer
     ↓
CurrentState
     ↓
PlayingState
PausedState
StoppedState
```

Changing state changes behavior.

---

## Structure

```text
Client
   |
Context
   |
State
   |
-----------------------
|          |          |
StateA   StateB    StateC
```

---

## Roles

### Context

The object whose behavior changes depending on its state.

---

### State

An interface that defines state-specific behavior.

---

### ConcreteState

Implements behavior for a specific state.

---

### Client

Uses the Context.

---

## Example 1 – Media Player

### State Interface

```php
interface PlayerState
{
    public function play(MediaPlayer $player): void;
    public function pause(MediaPlayer $player): void;
    public function stop(MediaPlayer $player): void;
}
```

---

### Context

```php
class MediaPlayer
{
    public function __construct(
        private PlayerState $state
    ) {}

    public function setState(PlayerState $state): void
    {
        $this->state = $state;
    }

    public function play(): void
    {
        $this->state->play($this);
    }

    public function pause(): void
    {
        $this->state->pause($this);
    }

    public function stop(): void
    {
        $this->state->stop($this);
    }
}
```

---

### StoppedState

```php
class StoppedState implements PlayerState
{
    public function play(MediaPlayer $player): void
    {
        echo 'Start playing' . PHP_EOL;
        $player->setState(new PlayingState());
    }

    public function pause(MediaPlayer $player): void
    {
        echo 'Cannot pause' . PHP_EOL;
    }

    public function stop(MediaPlayer $player): void
    {
        echo 'Already stopped' . PHP_EOL;
    }
}
```

---

### PlayingState

```php
class PlayingState implements PlayerState
{
    public function play(MediaPlayer $player): void
    {
        echo 'Already playing' . PHP_EOL;
    }

    public function pause(MediaPlayer $player): void
    {
        echo 'Paused' . PHP_EOL;
        $player->setState(new PausedState());
    }

    public function stop(MediaPlayer $player): void
    {
        echo 'Stopped' . PHP_EOL;
        $player->setState(new StoppedState());
    }
}
```

---

### PausedState

```php
class PausedState implements PlayerState
{
    public function play(MediaPlayer $player): void
    {
        echo 'Resume playing' . PHP_EOL;
        $player->setState(new PlayingState());
    }

    public function pause(MediaPlayer $player): void
    {
        echo 'Already paused' . PHP_EOL;
    }

    public function stop(MediaPlayer $player): void
    {
        echo 'Stopped' . PHP_EOL;
        $player->setState(new StoppedState());
    }
}
```

---

### Usage

```php
$player = new MediaPlayer(new StoppedState());

$player->play();
$player->pause();
$player->play();
$player->stop();
```

Output:

```text
Start playing
Paused
Resume playing
Stopped
```

---

## What Do We Gain?

Without State pattern:

```text
MediaPlayer
   ↓
large if/else logic
```

With State pattern:

```text
MediaPlayer
     ↓
PlayingState
PausedState
StoppedState
```

Each class handles its own behavior.

---

## Example 2 – Order System

States:

```text
Pending
Paid
Shipped
Delivered
```

---

### State Interface

```php
interface OrderState
{
    public function pay(Order $order): void;
}
```

---

### PendingState

```php
class PendingState implements OrderState
{
    public function pay(Order $order): void
    {
        echo 'Payment accepted' . PHP_EOL;
        $order->setState(new PaidState());
    }
}
```

---

### PaidState

```php
class PaidState implements OrderState
{
    public function pay(Order $order): void
    {
        echo 'Already paid' . PHP_EOL;
    }
}
```

---

### Context

```php
class Order
{
    public function __construct(
        private OrderState $state
    ) {}

    public function setState(OrderState $state): void
    {
        $this->state = $state;
    }

    public function pay(): void
    {
        $this->state->pay($this);
    }
}
```

---

### Usage

```php
$order = new Order(new PendingState());

$order->pay();
$order->pay();
```

Output:

```text
Payment accepted
Already paid
```

---

## Advantages

### 1. Eliminates large if/else blocks

Instead of:

```text
if (state == A)
if (state == B)
if (state == C)
```

you get:

```text
StateA
StateB
StateC
```

---

### 2. Follows Single Responsibility Principle (SRP)

Each state handles its own behavior:

```text
PlayingState → playing behavior
PausedState  → paused behavior
StoppedState → stopped behavior
```

---

### 3. Follows Open/Closed Principle (OCP)

You can add new states without modifying existing ones.

---

### 4. Easier maintenance

Each state is isolated and independent.

---

### 5. Explicit transitions

State flow becomes clear:

```text
Pending → Paid → Shipped → Delivered
```

---

## Disadvantages

### 1. Many classes

A system with many states can generate many classes.

---

### 2. Overengineering for simple cases

For simple logic like ON/OFF, a boolean may be enough.

---

### 3. Complex transitions

Large state machines can become difficult to manage.

---

## When to Use State Pattern

* Workflow systems
* Order processing systems
* Media players
* Game character behavior
* TCP connection states
* Document lifecycle systems

---

## When NOT to Use It

* Very few states
* Simple conditional logic
* Rare state changes

---

## Real-World Examples

### Traffic Light

```text
Red → Green → Yellow → Red
```

---

### Order Lifecycle

```text
Pending → Paid → Shipped → Delivered
```

---

### TCP Connection

```text
Closed → Listening → Established → Closing
```

---

## Laravel Example

Common use cases:

```text
Draft
Published
Archived
```

Instead of:

```php
if ($post->status === 'draft') { ... }
if ($post->status === 'published') { ... }
```

you can use:

```text
DraftState
PublishedState
ArchivedState
```

Packages like:

**spatie/laravel-model-states**

implement this pattern.

---

## State vs Strategy

### State

Behavior changes automatically based on internal state.

Example:

```text
Playing / Paused / Stopped
```

The object changes state itself.

---

### Strategy

You choose an algorithm.

Example:

```text
PayPal / Credit Card / Crypto
```

The client selects the strategy.

---

## State vs State Machine

### State Pattern

Focuses on behavior per state.

---

### State Machine

Includes states + transitions + events (broader concept).

---

## State vs Command

### State

Represents the current condition of an object.

---

### Command

Represents an action/request.

---

## Interview Summary

**State Pattern:**

> Allows an object to change its behavior when its internal state changes.

Key concepts:

```text
State Object
Context
Transitions
Behavior changes
Loose coupling
```

Used to replace large conditional logic with state classes that encapsulate behavior and transitions.

---
