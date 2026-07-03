# Mediator Design Pattern

## Definition

**Mediator** is a **behavioral design pattern** that reduces chaotic dependencies between objects by restricting direct communication between them and forcing them to collaborate only through a mediator object.

In other words, instead of objects talking directly to each other, they communicate through a central object called the **Mediator**.

---

## Problem it solves

Suppose we are building a GUI dialog:

We have:

```text
Button
TextBox
Checkbox
Dropdown
```

Without Mediator:

```text
Button → TextBox
Button → Checkbox
Button → Dropdown
Checkbox → Button
Checkbox → Dropdown
Dropdown → Button
...
```

We get:

```text
Many-to-Many Dependencies
```

or:

```text
A ↔ B
A ↔ C
A ↔ D
B ↔ C
B ↔ D
C ↔ D
```

The number of dependencies grows quickly.

Classes become:

* hard to maintain
* hard to test
* tightly coupled

---

## Idea

We introduce a central object:

```text
Button
   \
Checkbox ---> Mediator <--- TextBox
   /
Dropdown
```

Components do not communicate with each other directly.

They only call:

```php
$mediator->notify(...)
```

The Mediator decides:

```text
Who should react?
What should happen?
```

---

## Structure

```text
Client
   |
Mediator
   |
ConcreteMediator
   |
--------------------------
|           |            |
Colleague1 Colleague2 Colleague3
```

---

## Roles

### Mediator

Defines communication interface.

---

### ConcreteMediator

Implements interaction logic between components.

---

### Colleague

Component that communicates through the mediator.

---

### Client

Creates mediator and wires components.

---

## Example 1 – Dialog Window

### Mediator

```php
interface Mediator
{
    public function notify(
        Component $sender,
        string $event
    ): void;
}
```

---

### Base Component

```php
abstract class Component
{
    protected Mediator $mediator;

    public function setMediator(
        Mediator $mediator
    ): void {
        $this->mediator = $mediator;
    }
}
```

---

### Button

```php
class Button extends Component
{
    public function click(): void
    {
        echo "Button clicked" . PHP_EOL;

        $this->mediator->notify(
            $this,
            'click'
        );
    }
}
```

---

### TextBox

```php
class TextBox extends Component
{
    public function clear(): void
    {
        echo "Text cleared" . PHP_EOL;
    }
}
```

---

### Checkbox

```php
class Checkbox extends Component
{
    public function uncheck(): void
    {
        echo "Checkbox unchecked" . PHP_EOL;
    }
}
```

---

### Concrete Mediator

```php
class DialogMediator implements Mediator
{
    public function __construct(
        private Button $button,
        private TextBox $textBox,
        private Checkbox $checkbox
    ) {
        $button->setMediator($this);
        $textBox->setMediator($this);
        $checkbox->setMediator($this);
    }

    public function notify(
        Component $sender,
        string $event
    ): void {
        if ($event === 'click') {
            $this->textBox->clear();
            $this->checkbox->uncheck();
        }
    }
}
```

---

### Usage

```php
$button = new Button();
$textBox = new TextBox();
$checkbox = new Checkbox();

$dialog = new DialogMediator(
    $button,
    $textBox,
    $checkbox
);

$button->click();
```

Output:

```text
Button clicked
Text cleared
Checkbox unchecked
```

---

## What we gained

Without Mediator:

```text
Button → TextBox
Button → Checkbox
```

With Mediator:

```text
Button → Mediator → TextBox
                 → Checkbox
```

Components no longer know about each other.

---

## Example 2 – Chat Room

Without Mediator:

```text
UserA ↔ UserB
UserA ↔ UserC
UserB ↔ UserC
```

---

### Mediator

```php
interface ChatMediator
{
    public function send(
        string $message,
        User $sender
    ): void;
}
```

---

### Concrete Mediator

```php
class ChatRoom implements ChatMediator
{
    private array $users = [];

    public function add(User $user): void
    {
        $this->users[] = $user;
    }

    public function send(
        string $message,
        User $sender
    ): void {
        foreach ($this->users as $user) {
            if ($user !== $sender) {
                $user->receive($message);
            }
        }
    }
}
```

---

### User

```php
class User
{
    public function __construct(
        private string $name,
        private ChatMediator $chat
    ) {}

    public function send(string $message): void
    {
        $this->chat->send($message, $this);
    }

    public function receive(string $message): void
    {
        echo "{$this->name}: {$message}" . PHP_EOL;
    }
}
```

---

### Usage

```php
$room = new ChatRoom();

$john = new User('John', $room);
$alice = new User('Alice', $room);

$room->add($john);
$room->add($alice);

$john->send('Hello');
```

Output:

```text
Alice: Hello
```

---

## Visualization

Without Mediator:

```text
A ↔ B
A ↔ C
A ↔ D
B ↔ C
B ↔ D
C ↔ D
```

With Mediator:

```text
A
 \
  Mediator
 / | \
B  C  D
```

The number of dependencies is significantly reduced.

---

## Advantages

### 1. Reduces coupling

Instead of:

```text
A ↔ B ↔ C ↔ D
```

we get:

```text
A → Mediator
B → Mediator
C → Mediator
D → Mediator
```

---

### 2. Easier maintenance

Changes in interaction logic are centralized in the mediator.

---

### 3. Single Responsibility Principle (SRP)

Components:

```text
Button
TextBox
Checkbox
```

do their own job.

Coordination is handled by:

```text
Mediator
```

---

### 4. Open-Closed Principle (OCP)

We can add new components like:

```php
class Dropdown {}
```

and extend mediator logic without modifying existing components.

---

### 5. Easier testing

We can mock the mediator instead of wiring all components together.

---

## Disadvantages

### 1. Mediator can become a God Object

Over time:

```text
ApplicationMediator
├── users
├── orders
├── emails
├── notifications
├── reports
└── ...
```

---

### 2. Extra indirection

Instead of:

```text
Button → TextBox
```

we get:

```text
Button → Mediator → TextBox
```

---

### 3. Complex logic centralization

Mediator may become a large conditional block:

```text
if/else explosion
```

---

## When to use Mediator?

* GUI dialogs
* Chat systems
* Workflow coordination
* Event-driven systems
* When many objects communicate with each other
* To reduce tight coupling

---

## When NOT to use it?

* Few objects involved
* Simple direct communication is enough
* Mediator would become too complex

---

## Real-world example

### Air Traffic Control

Without mediator:

```text
Plane A ↔ Plane B
Plane A ↔ Plane C
Plane B ↔ Plane C
```

With mediator:

```text
Plane A
Plane B → Control Tower
Plane C
```

All communication goes through:

```text
Control Tower
```

---

## Chat Room example

```text
User1
User2
User3
   ↓
ChatRoom
```

Users send messages via mediator instead of directly to each other.

---

## Mediator vs Facade

### Facade

Focus:

```text
Hide complexity
```

Client uses simplified API:

```text
Subsystems → Facade
```

---

### Mediator

Focus:

```text
Coordinate communication
```

```text
Components → Mediator ↔ Components
```

---

## Mediator vs Observer

### Observer

```text
Publisher → Many Subscribers
```

One-to-many notification.

---

### Mediator

```text
Many Components → Mediator → Coordination
```

Central control of interactions.

---

## Mediator vs Chain of Responsibility

### Chain of Responsibility

```text
A → B → C → D
```

Request flows through chain.

---

### Mediator

```text
A
B
C
D
 ↓
Mediator
```

Central coordination instead of passing along a chain.

---

## Interview rule

**Mediator = Define an object that encapsulates how a set of objects interact.**

Key concepts:

```text
Centralized Communication
Loose Coupling
Coordination
Many-to-Many Simplification
Colleagues
```

Mediator is used when we want to:

```text
Eliminate direct dependencies
between many objects
and centralize their communication
into one mediator object.
```
