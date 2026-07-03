# Prototype Design Pattern

Prototype is a creational design pattern that lets you create new objects by copying an existing object (prototype) instead of creating them from scratch.

The pattern delegates the object creation process to the object itself by providing a mechanism for cloning.

Simply put:

> "Instead of creating a new complex object every time, create one fully configured object and clone it whenever you need another similar one."

---

## Why do we need Prototype?

Imagine we have a class whose creation is expensive because it:

* loads configuration from a database,
* parses large files,
* initializes many dependencies,
* or requires complex setup.

Without Prototype:

```php
$report1 = new Report();
$report2 = new Report();
$report3 = new Report();
```

Every object repeats the same expensive initialization.

With Prototype:

```php
$prototype = new Report();

$report1 = clone $prototype;
$report2 = clone $prototype;
$report3 = clone $prototype;
```

The expensive initialization happens only once.

---

## How does Prototype work?

Prototype usually consists of:

### 1. Prototype

An existing object that serves as a template.

### 2. Cloning

New objects are created by cloning the prototype.

### 3. Customization

After cloning, the copy can be modified if necessary.

---

## Structure

```text
Prototype
--------------------
+ clone()

ConcretePrototype
--------------------
+ clone()
```

The client creates new objects by copying an existing one instead of using `new`.

---

## Example 1 – Simple Cloning

```php
class Car
{
    public function __construct(
        public string $brand,
        public string $model
    ) {
    }
}
```

Usage:

```php
$car1 = new Car('BMW', 'X5');

$car2 = clone $car1;
$car2->model = 'X6';

var_dump($car1);
var_dump($car2);
```

Output:

```text
Car {
    brand => "BMW",
    model => "X5"
}

Car {
    brand => "BMW",
    model => "X6"
}
```

A new object is created by copying the existing one.

---

## Example 2 – Expensive Object Creation

Suppose a report loads configuration from a file or database:

```php
class Report
{
    public array $settings = [];

    public function __construct()
    {
        echo "Loading report settings..." . PHP_EOL;

        $this->settings = [
            'font' => 'Arial',
            'margin' => 20,
            'orientation' => 'portrait',
        ];
    }
}
```

Without Prototype:

```php
$report1 = new Report();
$report2 = new Report();
$report3 = new Report();
```

Output:

```text
Loading report settings...
Loading report settings...
Loading report settings...
```

With Prototype:

```php
$prototype = new Report();

$report1 = clone $prototype;
$report2 = clone $prototype;
$report3 = clone $prototype;
```

Output:

```text
Loading report settings...
```

The expensive initialization is performed only once.

---

## Custom Cloning

PHP provides a special method:

```php
public function __clone()
{
}
```

Example:

```php
class Document
{
    public string $title;

    public function __construct(string $title)
    {
        $this->title = $title;
    }

    public function __clone()
    {
        $this->title .= ' (Copy)';
    }
}
```

Usage:

```php
$doc1 = new Document('Invoice');

$doc2 = clone $doc1;

echo $doc1->title;
echo PHP_EOL;
echo $doc2->title;
```

Output:

```text
Invoice
Invoice (Copy)
```

---

## Shallow Copy

By default, PHP performs a **shallow copy**.

The cloned object becomes a new instance, but any objects stored inside it are still shared.

Example:

```php
class Engine
{
    public string $type = 'V8';
}

class Car
{
    public function __construct(
        public Engine $engine
    ) {
    }
}
```

```php
$car1 = new Car(new Engine());

$car2 = clone $car1;

$car2->engine->type = 'Electric';

echo $car1->engine->type;
```

Output:

```text
Electric
```

Why?

```text
car1 -----> Engine
car2 -----/
```

Both cars reference the same `Engine` object.

---

## Deep Copy

To create completely independent objects, we implement `__clone()`:

```php
class Car
{
    public function __construct(
        public Engine $engine
    ) {
    }

    public function __clone()
    {
        $this->engine = clone $this->engine;
    }
}
```

Now:

```php
$car1 = new Car(new Engine());

$car2 = clone $car1;

$car2->engine->type = 'Electric';

echo $car1->engine->type;
```

Output:

```text
V8
```

Now we have:

```text
car1 -----> Engine(V8)

car2 -----> Engine(Electric)
```

Each car owns its own `Engine`.

---

## Advantages

### 1. Better performance

If object creation is expensive:

```php
$copy = clone $prototype;
```

can be much faster than:

```php
$newObject = new ComplexObject();
```

---

### 2. Reduces object creation complexity

The client does not need to know:

* how the object is created,
* how it is configured,
* or which dependencies must be initialized.

---

### 3. Easy creation of similar objects

```php
$template = new Report();

$report1 = clone $template;
$report2 = clone $template;
$report3 = clone $template;
```

---

### 4. Dynamic registration of object types

Different prototypes can be registered:

```php
$prototypes['invoice'];
$prototypes['contract'];
$prototypes['report'];
```

and cloned whenever needed.

---

## Disadvantages

### 1. Deep Copy can become complicated

Especially when objects contain many nested objects.

---

### 2. Circular references

```text
A -> B
↑    ↓
└----┘
```

Deep cloning becomes much more difficult.

---

### 3. It may not be obvious what is copied and what is shared

Developers may expect a completely independent object while some internal references are still shared.

---

### 4. Additional maintenance

Whenever the object structure changes, the cloning logic inside:

```php
public function __clone()
{
    //
}
```

may also need to be updated.

---

## When should you use Prototype?

Use Prototype when:

* object creation is expensive,
* initialization requires a lot of configuration,
* you frequently create similar objects,
* you want to hide a complex creation process.

---

## When should you avoid it?

Avoid Prototype when:

* objects are simple:

```php
$user = new User();
```

* deep cloning becomes more complicated than creating a new object,
* object creation is cheap.

---

## Real-world example

Imagine a Microsoft Word template.

1. Create a document template.
2. Click **Duplicate**.
3. A copy is created with the same fonts, styles, and margins.
4. Change only the content.

This is exactly how the Prototype Pattern works.

---

## Interview Rule

**Prototype = Create new objects by cloning existing objects.**

The key idea:

```php
$prototype = new SomeObject();

$newObject = clone $prototype;
```

The most important concept in PHP is the difference between:

* **Shallow Copy** → nested objects are shared.
* **Deep Copy** → nested objects are cloned as well using `__clone()`.

That is why Prototype is most commonly implemented in PHP using the `clone` operator and, when necessary, the `__clone()` magic method.
