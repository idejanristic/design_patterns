# Flyweight Design Pattern

## Definition

**Flyweight** is a **structural design pattern** that lets you fit more objects into available memory by sharing common state between multiple objects instead of storing the same data in each object.

In other words, Flyweight reduces memory usage by sharing data that is common to many objects.

---

## Problem it solves

Imagine you are building a game with:

```text
1,000,000 Trees
```

Each tree contains:

```php
class Tree
{
    public string $name;
    public string $texture;
    public string $color;
    public int $x;
    public int $y;
}
```

If every tree stores:

* name
* texture
* color
* x
* y

you end up duplicating the same data a million times.

Example:

```text
Oak
oak.png
green
```

repeated 1,000,000 times.

This is a huge waste of memory.

---

## Idea

Split state into two parts:

### Intrinsic State (shared)

Data that is common and reusable:

* name
* texture
* color

---

### Extrinsic State (unique)

Data that changes per object:

* x
* y

---

Instead of:

```text
Tree
├── name
├── texture
├── color
├── x
└── y
```

We split it into:

```text
TreeType (shared)
├── name
├── texture
└── color

Tree (context)
├── x
├── y
└── TreeType
```

---

## Structure

```text
Client
   |
FlyweightFactory
   |
Flyweight
   |
ConcreteFlyweight
```

---

## Roles

### Flyweight

Stores intrinsic (shared) state.

### ConcreteFlyweight

Concrete implementation of flyweight object.

### FlyweightFactory

Creates and manages shared instances.

### Context

Stores extrinsic state and references flyweight.

---

## Example 1 – Forest Game

### Flyweight

```php
class TreeType
{
    public function __construct(
        public string $name,
        public string $texture,
        public string $color
    ) {}
}
```

---

### Context

```php
class Tree
{
    public function __construct(
        private int $x,
        private int $y,
        private TreeType $type
    ) {}

    public function draw(): void
    {
        echo "{$this->type->name} at {$this->x}, {$this->y}";
    }
}
```

---

### Flyweight Factory

```php
class TreeFactory
{
    private static array $types = [];

    public static function getTreeType(
        string $name,
        string $texture,
        string $color
    ): TreeType {
        $key = "{$name}-{$texture}-{$color}";

        if (!isset(self::$types[$key])) {
            self::$types[$key] = new TreeType(
                $name,
                $texture,
                $color
            );
        }

        return self::$types[$key];
    }
}
```

---

### Usage

```php
$oak = TreeFactory::getTreeType('Oak', 'oak.png', 'green');

$tree1 = new Tree(10, 20, $oak);
$tree2 = new Tree(30, 50, $oak);
$tree3 = new Tree(70, 90, $oak);
```

All trees share the same `TreeType`.

---

## What we achieved

Without Flyweight:

```text
Each Tree contains full data duplication
```

With Flyweight:

```text
Many Trees → one shared TreeType
```

---

## Example 2 – Text Editor

Each character has:

* font
* size
* color

Without Flyweight:

```text
H → Arial, 14, black
e → Arial, 14, black
l → Arial, 14, black
...
```

Same data repeated for every character.

---

### Flyweight

```php
class CharacterStyle
{
    public function __construct(
        public string $font,
        public int $size,
        public string $color
    ) {}
}
```

---

### Context

```php
class Character
{
    public function __construct(
        public string $symbol,
        public int $position,
        public CharacterStyle $style
    ) {}
}
```

Many characters share the same `CharacterStyle`.

---

## Advantages

### 1. Reduces memory usage significantly

Instead of duplicating data, it is shared.

---

### 2. Improves performance

Less object creation and GC pressure.

---

### 3. Efficient for large numbers of similar objects

Useful for:

* games
* rendering systems
* text editors
* maps

---

### 4. Centralized control of shared objects

Factory manages reuse.

---

## Disadvantages

### 1. Increased complexity

You must separate intrinsic and extrinsic state.

---

### 2. Harder debugging

State is split across multiple objects.

---

### 3. Not useful for small number of objects

Overhead is not worth it.

---

## When to use Flyweight?

* huge number of similar objects
* high memory usage
* shared state between objects
* immutable shared data

---

## When NOT to use it?

* small datasets
* mostly unique objects
* when simplicity is more important than optimization

---

## Real-world example

### Game engine

Thousands of trees:

* shared: model, texture, color
* unique: position, rotation

---

### Word processor

Characters:

* shared: font, size, style
* unique: position, symbol

---

## Difference: Flyweight vs Singleton

### Singleton

One instance globally.

### Flyweight

Many objects sharing internal state.

---

## Difference: Flyweight vs Prototype

### Prototype

Creates new objects by cloning.

### Flyweight

Avoids duplication by sharing objects.

---

## Interview definition

**Flyweight is a structural design pattern that minimizes memory usage by sharing as much data as possible between similar objects.**

---

## Key idea

```text
Share intrinsic state
Move extrinsic state outside
Reuse objects instead of duplicating them
```
