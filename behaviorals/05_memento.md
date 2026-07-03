# Memento Design Pattern

## Definition (English)

**Memento** is a **behavioral design pattern** that lets you save and restore the previous state of an object without revealing the details of its implementation.

In other words, Memento allows an object to create a snapshot of its state and restore itself from that snapshot later.

---

## Problem it solves

Suppose we have a text editor.

```php
class TextEditor
{
    private string $text = '';

    public function write(
        string $text
    ): void {
        $this->text .= $text;
    }

    public function getText(): string
    {
        return $this->text;
    }
}
```

We want:

```text
Write text
Write more text
Undo
Redo
```

Without the Memento pattern, we would have to:

```php
$history[] = $editor->getText();
```

Problem:

* the client must know the internal structure of the object,
* if the state changes, client code must also change,
* encapsulation is violated.

---

## Idea

The object itself creates a snapshot of its state.

Instead of:

```text
Client
   ↓
Editor internals
```

we get:

```text
Client
   ↓
Memento
   ↓
Editor
```

The client does not know what is inside the snapshot.

It only knows:

```php
$editor->save();
$editor->restore($memento);
```

---

## Structure

```text
Client
   |
Caretaker
   |
Memento
   |
Originator
```

---

## Roles

### Originator

The object whose state we want to save.

---

### Memento

A snapshot of the object’s state.

---

### Caretaker

Stores memento objects.

---

### Client

Uses the Originator and Caretaker.

---

## Example 1 – Text Editor Undo

### Memento

```php
class EditorMemento
{
    public function __construct(
        private string $text
    ) {
    }

    public function getText(): string
    {
        return $this->text;
    }
}
```

---

### Originator

```php
class TextEditor
{
    private string $text = '';

    public function write(
        string $text
    ): void {
        $this->text .= $text;
    }

    public function getText(): string
    {
        return $this->text;
    }

    public function save(): EditorMemento
    {
        return new EditorMemento(
            $this->text
        );
    }

    public function restore(
        EditorMemento $memento
    ): void {
        $this->text =
            $memento->getText();
    }
}
```

---

### Caretaker

```php
class History
{
    private array $states = [];

    public function push(
        EditorMemento $memento
    ): void {
        $this->states[] = $memento;
    }

    public function pop(): ?EditorMemento
    {
        return array_pop(
            $this->states
        );
    }
}
```

---

### Usage

```php
$editor = new TextEditor();
$history = new History();

$editor->write('Hello ');
$history->push(
    $editor->save()
);

$editor->write('World');

echo $editor->getText();
```

Output:

```text
Hello World
```

Undo:

```php
$editor->restore(
    $history->pop()
);

echo $editor->getText();
```

Output:

```text
Hello
```

---

## What did we achieve?

Editor:

```text
knows how to save itself
knows how to restore itself
```

Client:

```text
does not know editor internals
```

Encapsulation is preserved.

---

## Visual

```text
TextEditor
      ↓ save()
EditorMemento
      ↓ stored in
History
      ↓ restore()
TextEditor
```

---

## Example 2 – Game Save System

### Memento

```php
class GameMemento
{
    public function __construct(
        private int $level,
        private int $health
    ) {
    }

    public function getLevel(): int
    {
        return $this->level;
    }

    public function getHealth(): int
    {
        return $this->health;
    }
}
```

---

### Originator

```php
class Game
{
    public function __construct(
        private int $level,
        private int $health
    ) {
    }

    public function save(): GameMemento
    {
        return new GameMemento(
            $this->level,
            $this->health
        );
    }

    public function restore(
        GameMemento $memento
    ): void {
        $this->level =
            $memento->getLevel();

        $this->health =
            $memento->getHealth();
    }

    public function info(): void
    {
        echo "Level {$this->level},
              Health {$this->health}";
    }
}
```

---

### Usage

```php
$game = new Game(5, 100);

$save = $game->save();

$game = new Game(10, 20);

$game->info();
```

Output:

```text
Level 10, Health 20
```

Restore:

```php
$game->restore($save);

$game->info();
```

Output:

```text
Level 5, Health 100
```

---

## Advantages

### 1. Encapsulation is preserved

Client does not know:

```text
private properties
internal structure
implementation details
```

The object manages its own state.

---

### 2. Undo/Redo functionality

Memento is ideal for:

```text
Text editors
Graphics editors
Games
Forms
```

---

### 3. Easy state restoration

```php
$object->restore($snapshot);
```

---

### 4. Single Responsibility Principle (SRP)

```text
Originator → business state
Memento → snapshot
Caretaker → history management
```

---

### 5. Checkpoints

We can store:

```text
State 1
State 2
State 3
State 4
```

and restore any of them.

---

## Disadvantages

### 1. High memory usage

If we have:

```text
1000 snapshots
```

and each snapshot is large:

```text
10 MB
```

memory usage becomes very high.

---

### 2. Hard history management

We must manage:

```text
undo stack
redo stack
cleanup
limits
```

---

### 3. Expensive snapshots

If the object contains:

```text
large collections
large graphs
nested objects
```

creating snapshots can be expensive.

---

### 4. Deep copy issues

If the state contains references:

```php
class User
{
    private Address $address;
}
```

we must handle:

```text
shallow copy vs deep copy
```

---

## When to use Memento?

✅ Undo/Redo
✅ Save games
✅ Draft systems
✅ Transaction rollback
✅ Checkpoints
✅ Version history

---

## When to avoid it?

❌ Object has huge state
❌ Snapshots are too expensive
❌ No need to restore previous state

---

## Real-world examples

### Microsoft Word

```text
Write → Ctrl+Z → previous state
```

Word stores document snapshots.

---

### Photoshop

```text
Draw → filter → undo → redo
```

Each action can be stored as a snapshot.

---

### Video games

```text
Checkpoint → death → restore checkpoint
```

---

## Difference between Memento and Command

### Memento

Focus:

```text
Save object state
```

---

### Command

Focus:

```text
Encapsulate action
```

---

## Difference between Memento and Prototype

### Prototype

```text
Clone entire object
```

---

### Memento

```text
Store state for later restore
```

---

## Difference between Memento and Event Sourcing

### Memento

```text
State snapshots
```

---

### Event Sourcing

```text
Store events
```

and reconstruct state from events.

---

## Interview rule

**Memento = Capture and restore an object's internal state without violating encapsulation.**

Key idea:

```php
class Originator
{
    public function save(): Memento
    {
        return new Memento($this->state);
    }

    public function restore(Memento $memento): void
    {
        $this->state = $memento->getState();
    }
}
```

Most important concepts:

```text
Snapshot
Undo/Redo
History
Encapsulation
Checkpoint
Restore
```

Memento is used when we want to:

```text
save an object's state,
restore it later,
without exposing its internal structure.
```
