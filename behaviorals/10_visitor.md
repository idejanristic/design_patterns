# Visitor Design Pattern

## Definition

**Visitor** is a **behavioral design pattern** that lets you separate algorithms from the objects on which they operate.

In other words, Visitor allows you to add new operations to existing classes **without modifying those classes**.

---

## Problem It Solves

Let’s assume we have these shapes:

```text id="a1k9qp"
Circle
Rectangle
Triangle
```

We want to support multiple operations:

* export to JSON
* export to XML
* calculate area
* render

### Without Visitor Pattern:

```php id="m3x8ld"
class Circle
{
    public function area() {}
    public function toJson() {}
    public function toXml() {}
    public function render() {}
}

class Rectangle
{
    public function area() {}
    public function toJson() {}
    public function toXml() {}
    public function render() {}
}
```

### Problems:

* classes become bloated,
* violates Single Responsibility Principle,
* every new operation requires modifying all classes.

---

## Idea

Keep objects simple:

```text id="c9v2ld"
Circle
Rectangle
Triangle
```

Move operations into separate Visitor classes:

```text id="q7m1xp"
AreaVisitor
JsonVisitor
XmlVisitor
RenderVisitor
```

Objects only expose:

```php id="h2k9zd"
$shape->accept($visitor);
```

The visitor performs the operation.

---

## Structure

```text id="t8k3qp"
Client
   |
Visitor
   |
---------------------
|         |         |
Circle Rectangle Triangle
```

---

## Roles

### Visitor

Defines operations for each concrete element type.

---

### Concrete Visitor

Implements specific operations.

---

### Element

Declares:

```php
accept(Visitor $visitor)
```

---

### Concrete Element

Implements `accept()`.

---

### Client

Creates and applies visitors.

---

## Example 1 – Shapes

### Visitor Interface

```php id="v3k8ld"
interface ShapeVisitor
{
    public function visitCircle(Circle $circle): void;
    public function visitRectangle(Rectangle $rectangle): void;
}
```

---

### Element Interface

```php id="z2m9qp"
interface Shape
{
    public function accept(ShapeVisitor $visitor): void;
}
```

---

### Circle

```php id="p7x1ld"
class Circle implements Shape
{
    public function __construct(
        private int $radius
    ) {}

    public function getRadius(): int
    {
        return $this->radius;
    }

    public function accept(ShapeVisitor $visitor): void
    {
        $visitor->visitCircle($this);
    }
}
```

---

### Rectangle

```php id="r9k2xp"
class Rectangle implements Shape
{
    public function __construct(
        private int $width,
        private int $height
    ) {}

    public function getWidth(): int
    {
        return $this->width;
    }

    public function getHeight(): int
    {
        return $this->height;
    }

    public function accept(ShapeVisitor $visitor): void
    {
        $visitor->visitRectangle($this);
    }
}
```

---

### Area Visitor

```php id="a8k2ld"
class AreaVisitor implements ShapeVisitor
{
    public function visitCircle(Circle $circle): void
    {
        $area = pi() * $circle->getRadius() ** 2;

        echo "Circle area: {$area}" . PHP_EOL;
    }

    public function visitRectangle(Rectangle $rectangle): void
    {
        $area = $rectangle->getWidth() * $rectangle->getHeight();

        echo "Rectangle area: {$area}" . PHP_EOL;
    }
}
```

---

### JSON Visitor

```php id="x2m8qp"
class JsonVisitor implements ShapeVisitor
{
    public function visitCircle(Circle $circle): void
    {
        echo json_encode([
            'type' => 'circle',
            'radius' => $circle->getRadius()
        ]) . PHP_EOL;
    }

    public function visitRectangle(Rectangle $rectangle): void
    {
        echo json_encode([
            'type' => 'rectangle',
            'width' => $rectangle->getWidth(),
            'height' => $rectangle->getHeight()
        ]) . PHP_EOL;
    }
}
```

---

### Usage

```php id="k7p1ld"
$shapes = [
    new Circle(10),
    new Rectangle(5, 8),
];

$visitor = new AreaVisitor();

foreach ($shapes as $shape) {
    $shape->accept($visitor);
}
```

Output:

```text id="n3x8qp"
Circle area: 314.159...
Rectangle area: 40
```

---

## What Do We Gain?

Without Visitor:

```text id="v8k2ld"
Circle
├── area()
├── json()
├── xml()
└── render()
```

With Visitor:

```text id="c2m9xp"
Circle → accepts Visitor
Rectangle → accepts Visitor

Visitors:
AreaVisitor
JsonVisitor
XmlVisitor
RenderVisitor
```

Operations are separated from data.

---

## Double Dispatch

Visitor uses **double dispatch**:

1. First call:

```php id="d9k2ld"
$shape->accept($visitor);
```

2. Then:

```php id="f8m3xp"
$visitor->visitCircle($shape);
```

Method resolution depends on:

* object type (Circle, Rectangle)
* visitor type (AreaVisitor, JsonVisitor)

---

## Example 2 – File System

We may have:

```text id="h7k2qp"
File
Directory
Shortcut
```

We want operations like:

* size calculation
* search
* backup
* permissions report

We create:

```text id="q9m2ld"
SizeVisitor
SearchVisitor
BackupVisitor
PermissionsVisitor
```

No need to modify existing classes.

---

## Advantages

### 1. Single Responsibility Principle (SRP)

* Circle → data only
* AreaVisitor → calculations
* JsonVisitor → serialization

---

### 2. Open/Closed Principle (OCP)

You can add new visitors without changing existing classes.

---

### 3. Centralized logic

Instead of scattering logic:

```text id="a2k8qp"
Circle::toJson()
Rectangle::toJson()
Triangle::toJson()
```

You have:

```text id="m7x2ld"
JsonVisitor
```

---

### 4. Works well with complex structures

* AST trees
* DOM trees
* file systems
* compilers

---

## Disadvantages

### 1. Hard to add new element types

If you add:

```php id="t2k9ld"
class Triangle
```

you must update all visitors:

* AreaVisitor
* JsonVisitor
* RenderVisitor
* etc.

---

### 2. Many classes

Combinations of elements × operations can grow quickly.

---

### 3. Breaks encapsulation

Visitors often need internal data via getters.

---

## When to Use Visitor

* compilers / AST processing
* file systems
* document structures
* reporting engines
* stable object hierarchy with many operations

---

## When NOT to Use It

* frequently changing object types
* few operations
* simple systems

---

## Real-World Example

### Compiler AST

```text id="c9x2ld"
AddExpression
MultiplyExpression
NumberExpression
```

Visitors:

* TypeCheckVisitor
* OptimizeVisitor
* CodeGenVisitor
* PrintVisitor

---

## Laravel Context

Visitor is not heavily used directly, but similar ideas appear in:

* AST processing
* configuration traversal
* documentation generation
* structured data processing

---

## Visitor vs Strategy

### Strategy

* choose one algorithm
* interchangeable behavior

### Visitor

* apply operations to many object types
* structured traversal

---

## Visitor vs Command

### Command

* represents an action/request

### Visitor

* represents operations over object structure

---

## Visitor vs Template Method

### Template Method

* inheritance-based fixed algorithm

### Visitor

* composition + double dispatch

---

## Visitor vs Iterator

### Iterator

* how to traverse

### Visitor

* what to do during traversal

---

## Interview Summary

**Visitor Pattern:**

> Separates algorithms from the objects on which they operate.

Key concepts:

```text id="v7k2ld"
Visitor
Element
Accept
Double Dispatch
Operation Separation
Object Structure
```

Use Visitor when you want to add new operations to a stable object structure without modifying existing classes.
