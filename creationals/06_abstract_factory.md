# Abstract Factory Pattern

## Definition

**Abstract Factory** is a **creational design pattern** that provides an interface for creating **families of related or dependent objects** without specifying their concrete classes.

In other words, Abstract Factory lets you create entire groups of related objects while keeping the client independent of their concrete implementations.

---

## What does that mean?

The **Abstract Factory** pattern groups together objects that belong to the same family and ensures they are created consistently.

Instead of creating each object individually with `new`, the client asks a factory to create all related objects.

The client works only with **interfaces (abstract products)** and **doesn't know which concrete classes are being instantiated**.

Simply put:

> "One factory creates an entire family of compatible objects."

---

## Why do we need Abstract Factory?

Imagine you're developing a GUI library that supports multiple operating systems:

* Windows
* macOS

Each platform provides its own:

* Button
* Checkbox

Without Abstract Factory:

```php
$button = new WindowsButton();
$checkbox = new MacCheckbox();
```

Nothing prevents us from accidentally mixing products from different families:

```text
Windows Button
Mac Checkbox
```

This may lead to inconsistent behavior or appearance.

With Abstract Factory:

```php
$factory = new WindowsFactory();

$button = $factory->createButton();
$checkbox = $factory->createCheckbox();
```

Both objects always belong to the same family.

---

## The idea

Instead of creating every object manually:

```php
new WindowsButton();
new WindowsCheckbox();
```

we use a factory:

```php
$factory = new WindowsFactory();

$button = $factory->createButton();
$checkbox = $factory->createCheckbox();
```

The factory guarantees that all created objects are compatible with each other.

---

## How Abstract Factory works

The pattern usually consists of four parts.

### 1. Abstract Factory

Defines methods for creating each product in a family.

Example:

```php
interface GuiFactory
{
    public function createButton(): Button;

    public function createCheckbox(): Checkbox;
}
```

---

### 2. Concrete Factory

Creates one specific family of products.

Example:

```php
class WindowsFactory implements GuiFactory
{
}
```

or

```php
class MacFactory implements GuiFactory
{
}
```

---

### 3. Abstract Products

Define the common interfaces for each product type.

Example:

```php
interface Button
{
}

interface Checkbox
{
}
```

---

### 4. Concrete Products

Implement the abstract product interfaces.

Example:

```php
class WindowsButton implements Button
{
}

class MacButton implements Button
{
}
```

---

## Structure

```text
                  Abstract Factory
          ┌────────────────────────────┐
          │ createProductA()           │
          │ createProductB()           │
          └─────────────┬──────────────┘
                        │
        ┌───────────────┴───────────────┐
        │                               │
 ConcreteFactoryA              ConcreteFactoryB
        │                               │
   ┌────┴────┐                    ┌─────┴─────┐
   │         │                    │           │
ProductA1 ProductB1          ProductA2 ProductB2
```

Each concrete factory creates one complete family of related products.

---

## Example 1 – GUI Library

### Abstract Products

```php
interface Button
{
    public function render(): void;
}

interface Checkbox
{
    public function render(): void;
}
```

---

### Concrete Products

Windows:

```php
class WindowsButton implements Button
{
    public function render(): void
    {
        echo 'Rendering Windows Button';
    }
}

class WindowsCheckbox implements Checkbox
{
    public function render(): void
    {
        echo 'Rendering Windows Checkbox';
    }
}
```

macOS:

```php
class MacButton implements Button
{
    public function render(): void
    {
        echo 'Rendering Mac Button';
    }
}

class MacCheckbox implements Checkbox
{
    public function render(): void
    {
        echo 'Rendering Mac Checkbox';
    }
}
```

---

### Abstract Factory

```php
interface GuiFactory
{
    public function createButton(): Button;

    public function createCheckbox(): Checkbox;
}
```

---

### Concrete Factories

```php
class WindowsFactory implements GuiFactory
{
    public function createButton(): Button
    {
        return new WindowsButton();
    }

    public function createCheckbox(): Checkbox
    {
        return new WindowsCheckbox();
    }
}

class MacFactory implements GuiFactory
{
    public function createButton(): Button
    {
        return new MacButton();
    }

    public function createCheckbox(): Checkbox
    {
        return new MacCheckbox();
    }
}
```

---

### Usage

```php
function renderUi(GuiFactory $factory): void
{
    $button = $factory->createButton();
    $checkbox = $factory->createCheckbox();

    $button->render();
    $checkbox->render();
}

renderUi(new WindowsFactory());
renderUi(new MacFactory());
```

Output:

```text
Rendering Windows Button
Rendering Windows Checkbox

Rendering Mac Button
Rendering Mac Checkbox
```

---

## Why is this better?

The client works only with:

```php
GuiFactory
Button
Checkbox
```

and knows nothing about:

```php
WindowsButton
MacButton
WindowsCheckbox
MacCheckbox
```

This provides:

* loose coupling
* compatible product families
* easy replacement of the entire family

---

## Example 2 – Database Drivers

Suppose our application supports:

* MySQL
* PostgreSQL

Each database provides:

* Connection
* QueryBuilder

---

### Abstract Products

```php
interface Connection
{
    public function connect(): void;
}

interface QueryBuilder
{
    public function select(string $table): void;
}
```

---

### MySQL Family

```php
class MySqlConnection implements Connection
{
    public function connect(): void
    {
        echo 'Connecting to MySQL';
    }
}

class MySqlQueryBuilder implements QueryBuilder
{
    public function select(string $table): void
    {
        echo "SELECT * FROM {$table}";
    }
}
```

---

### PostgreSQL Family

```php
class PostgreSqlConnection implements Connection
{
    public function connect(): void
    {
        echo 'Connecting to PostgreSQL';
    }
}

class PostgreSqlQueryBuilder implements QueryBuilder
{
    public function select(string $table): void
    {
        echo "SELECT * FROM {$table}";
    }
}
```

---

### Abstract Factory

```php
interface DatabaseFactory
{
    public function createConnection(): Connection;

    public function createQueryBuilder(): QueryBuilder;
}
```

---

### Concrete Factories

```php
class MySqlFactory implements DatabaseFactory
{
    public function createConnection(): Connection
    {
        return new MySqlConnection();
    }

    public function createQueryBuilder(): QueryBuilder
    {
        return new MySqlQueryBuilder();
    }
}

class PostgreSqlFactory implements DatabaseFactory
{
    public function createConnection(): Connection
    {
        return new PostgreSqlConnection();
    }

    public function createQueryBuilder(): QueryBuilder
    {
        return new PostgreSqlQueryBuilder();
    }
}
```

---

## Advantages

### 1. Guarantees Product Compatibility

```text
WindowsFactory
├── WindowsButton
└── WindowsCheckbox
```

The factory ensures that compatible products are created together.

You cannot accidentally combine:

```text
WindowsButton
MacCheckbox
```

unless you intentionally bypass the factory.

---

### 2. Loose Coupling

The client depends only on:

```php
GuiFactory
Button
Checkbox
```

instead of concrete implementations.

---

### 3. Easy Family Replacement

Changing:

```php
$factory = new WindowsFactory();
```

to:

```php
$factory = new MacFactory();
```

switches the entire family of products without changing client code.

---

### 4. Supports the Open-Closed Principle (OCP)

Adding a new family:

```text
Linux Factory
├── LinuxButton
└── LinuxCheckbox
```

does not require modifying existing client code.

---

### 5. Works Well with Dependency Injection

Factories such as:

```php
GuiFactory
DatabaseFactory
PaymentFactory
```

can easily be injected into services, making implementations interchangeable.

---

## Disadvantages

### 1. Many Classes

Each family usually requires:

```text
Factory
├── ProductA
└── ProductB
```

The number of classes grows quickly.

---

### 2. Difficult to Add New Product Types

Suppose we introduce:

```php
Slider
```

We must modify:

```php
GuiFactory
WindowsFactory
MacFactory
LinuxFactory
...
```

and implement:

```php
createSlider()
```

everywhere.

Adding a **new family** is easy.

Adding a **new product type** is difficult.

---

### 3. Increased Complexity

For small applications:

```php
new Button();
```

is much simpler than introducing multiple factories and interfaces.

---

## When should you use Abstract Factory?

Use it when:

* there are multiple families of related objects
* products within each family must always work together
* you want to switch entire product families easily
* you want to program against interfaces rather than implementations

---

## When should you avoid it?

Avoid it when:

* there is only one product type
* there are no product families
* the number of products is very small and unlikely to change

---

## Real-world example

Imagine a car manufacturer.

### BMW Factory

```text
BMW Factory
├── BMW Engine
├── BMW Transmission
└── BMW Dashboard
```

### Audi Factory

```text
Audi Factory
├── Audi Engine
├── Audi Transmission
└── Audi Dashboard
```

The client works only with:

```php
CarFactory
Engine
Transmission
Dashboard
```

and doesn't care whether the components come from BMW or Audi.

---

## Difference between Factory Method and Abstract Factory

| Feature         | Factory Method      | Abstract Factory             |
| --------------- | ------------------- | ---------------------------- |
| Creates         | One product         | A family of related products |
| Factory methods | One                 | Multiple                     |
| Complexity      | Lower               | Higher                       |
| Typical example | NotificationFactory | GUI Factory                  |

---

## Difference from Simple Factory

**Simple Factory**

One factory creates different products based on input parameters.

```php
NotificationFactory::make('email');
```

---

**Factory Method**

Each subclass decides which single product to create.

```php
EmailFactory
SmsFactory
PushFactory
```

---

**Abstract Factory**

One factory creates multiple related products that belong to the same family.

```text
WindowsFactory
├── WindowsButton
└── WindowsCheckbox
```

---

## Interview Rule

> **Abstract Factory = Provide an interface for creating families of related objects without specifying their concrete classes.**

The key idea is:

```php
interface Factory
{
    public function createProductA(): ProductA;

    public function createProductB(): ProductB;
}
```

The client works only with:

```php
Factory
ProductA
ProductB
```

and remains completely independent of:

```php
ConcreteFactory
ConcreteProductA
ConcreteProductB
```

As a result, Abstract Factory guarantees that related objects are created together, reduces coupling between the client and concrete implementations, makes it easy to switch entire product families, and fits naturally with the **Open-Closed Principle (OCP)** and the **Dependency Inversion Principle (DIP)**.
