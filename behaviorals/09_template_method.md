# Template Method Design Pattern

## Definition

**Template Method** is a **behavioral design pattern** that defines the skeleton of an algorithm in a base class and lets subclasses override specific steps without changing the overall structure of the algorithm.

In other words:

* the parent class defines **what steps are executed and in what order**,
* subclasses define **how certain steps are implemented**.

---

## Problem It Solves

Let’s say we are building a report generation system.

The steps are:

```text id="w7k9qa"
Read Data
Process Data
Export Report
```

But there are multiple formats:

* PDF
* CSV
* Excel

### Without Template Method:

```php id="m2x8lc"
class PdfReport
{
    public function generate(): void
    {
        $this->readData();
        $this->processData();
        $this->exportPdf();
    }
}

class CsvReport
{
    public function generate(): void
    {
        $this->readData();
        $this->processData();
        $this->exportCsv();
    }
}
```

### Problems:

* code duplication,
* hard to maintain,
* algorithm is repeated across multiple classes.

---

## Idea

We move the common algorithm into a base class:

```text id="9v3nqk"
generate()
   ↓
readData()
processData()
export()
```

Subclasses only implement the variable part:

```text id="xq7t1p"
export()
```

---

## Structure

```text id="k8m3ld"
Client
   |
AbstractClass
   |
-----------------------
|                     |
ConcreteClassA   ConcreteClassB
```

---

## Roles

### Abstract Class

Defines the template method and common steps.

---

### Template Method

Defines:

* which steps are executed,
* in what order.

---

### Concrete Class

Implements specific steps.

---

### Client

Uses concrete implementations.

---

## Example 1 – Report Generator

### Abstract Class

```php id="r4k9zv"
abstract class ReportGenerator
{
    final public function generate(): void
    {
        $this->readData();
        $this->processData();
        $this->export();
    }

    protected function readData(): void
    {
        echo "Reading data" . PHP_EOL;
    }

    protected function processData(): void
    {
        echo "Processing data" . PHP_EOL;
    }

    abstract protected function export(): void;
}
```

---

### PDF Report

```php id="c9x2pq"
class PdfReport extends ReportGenerator
{
    protected function export(): void
    {
        echo "Exporting PDF" . PHP_EOL;
    }
}
```

---

### CSV Report

```php id="t7k1zd"
class CsvReport extends ReportGenerator
{
    protected function export(): void
    {
        echo "Exporting CSV" . PHP_EOL;
    }
}
```

---

### Usage

```php id="n6p2wr"
$report = new PdfReport();
$report->generate();
```

Output:

```text id="h1q8xz"
Reading data
Processing data
Exporting PDF
```

---

## What Do We Gain?

Without Template Method:

```text id="v9k2aa"
PdfReport → read/process/export
CsvReport → read/process/export
```

With Template Method:

```text id="b3m7ld"
ReportGenerator
   ↓
generate()
   ↓
read → process → export()
```

Only one step varies.

---

## Example 2 – Beverage Preparation

Steps:

```text id="z8p1kx"
Boil Water
Brew
Pour Into Cup
Add Condiments
```

---

### Abstract Class

```php id="d2x9mv"
abstract class Beverage
{
    final public function prepare(): void
    {
        $this->boilWater();
        $this->brew();
        $this->pourIntoCup();
        $this->addCondiments();
    }

    private function boilWater(): void
    {
        echo "Boiling water" . PHP_EOL;
    }

    private function pourIntoCup(): void
    {
        echo "Pouring into cup" . PHP_EOL;
    }

    abstract protected function brew(): void;
    abstract protected function addCondiments(): void;
}
```

---

### Tea

```php id="p3k8xa"
class Tea extends Beverage
{
    protected function brew(): void
    {
        echo "Steeping tea" . PHP_EOL;
    }

    protected function addCondiments(): void
    {
        echo "Adding lemon" . PHP_EOL;
    }
}
```

---

### Coffee

```php id="q1v7nb"
class Coffee extends Beverage
{
    protected function brew(): void
    {
        echo "Brewing coffee" . PHP_EOL;
    }

    protected function addCondiments(): void
    {
        echo "Adding sugar and milk" . PHP_EOL;
    }
}
```

---

### Usage

```php id="f8m2ld"
$tea = new Tea();
$tea->prepare();

echo PHP_EOL;

$coffee = new Coffee();
$coffee->prepare();
```

Output:

```text id="x7n3qa"
Boiling water
Steeping tea
Pouring into cup
Adding lemon

Boiling water
Brewing coffee
Pouring into cup
Adding sugar and milk
```

---

## Hooks (Optional Steps)

Template Method can include **hook methods**.

```php id="h9k2ld"
abstract class Report
{
    final public function generate(): void
    {
        $this->read();

        if ($this->shouldLog()) {
            $this->log();
        }

        $this->export();
    }

    protected function shouldLog(): bool
    {
        return false;
    }

    protected function log(): void
    {
        echo "Logging";
    }

    abstract protected function export(): void;
}
```

Subclass:

```php id="c7x2pq"
protected function shouldLog(): bool
{
    return true;
}
```

---

## Advantages

### 1. Eliminates code duplication

Common steps exist only once.

---

### 2. Follows DRY principle

Shared logic is not repeated.

---

### 3. Controls the algorithm structure

The base class defines the execution order.

---

### 4. Follows Open/Closed Principle

You can add new implementations without changing existing code.

---

### 5. Hollywood Principle

> “Don’t call us, we’ll call you.”

The base class calls subclass methods.

---

## Disadvantages

### 1. Uses inheritance

Creates tight coupling between parent and child.

---

### 2. Limited flexibility

Subclasses cannot easily change the algorithm structure.

---

### 3. Deep hierarchies

Can lead to complex inheritance trees.

---

### 4. Risk of LSP violations

If subclasses must throw exceptions for steps they don’t support, design may be wrong.

---

## When to Use Template Method

* report generation
* import/export systems
* file processing
* ETL pipelines
* framework lifecycles
* game loops

---

## When NOT to Use It

* frequent algorithm changes
* high variability in behavior
* composition is more appropriate than inheritance

---

## Real-World Examples

### E-commerce Checkout

```text id="a2k8ld"
Validate Cart
Calculate Total
Process Payment
Send Confirmation
```

Payment can vary, but structure remains the same.

---

### Build Pipelines

```text id="m8x2qp"
Compile
Test
Package
Deploy
```

Same structure, different implementations per project.

---

## Laravel Examples

### Migrations

Laravel defines the flow:

```text id="v3k9ld"
Run Migration → up()
Rollback → down()
```

---

### Seeders

```php id="t2x8qp"
class UserSeeder extends Seeder
{
    public function run()
    {
        //
    }
}
```

Framework controls execution order.

---

### Console Commands

```php id="n9k2ld"
class SendEmails extends Command
{
    public function handle()
    {
        //
    }
}
```

Framework calls `handle()` → Template Method style.

---

## Template Method vs Strategy

### Template Method

* uses inheritance
* defines fixed algorithm structure

### Strategy

* uses composition
* swaps entire algorithms

---

## Template Method vs Factory Method

### Template Method

* defines algorithm skeleton

### Factory Method

* focuses on object creation

Often used together.

---

## Template Method vs State

### Template Method

* fixed sequence of steps

### State

* behavior depends on current state

---

## Interview Summary

**Template Method:**

> Defines the skeleton of an algorithm in a base class and lets subclasses override specific steps without changing the structure.

Key concepts:

```text id="k3m9ld"
Algorithm skeleton
Inheritance
Hooks
Fixed sequence
Code reuse
Hollywood principle
```

Used when you want to keep the algorithm structure fixed while allowing subclasses to customize certain steps.
