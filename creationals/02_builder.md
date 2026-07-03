# The Builder Design Pattern

The **Builder** is a **creational design pattern** that allows you to **construct complex objects step by step**. It enables you to create **different representations or configurations of an object** using the **same construction process**.

Simply put:

> **Instead of passing everything into a large constructor, build the object one step at a time.**

---

## Why Do We Need the Builder Pattern?

Imagine we have a `Car` class.

A car may have:

* an engine,
* a number of doors,
* air conditioning,
* navigation,
* an automatic transmission,
* a sunroof,
* a color.

Without the Builder pattern:

```php
class Car
{
    public function __construct(
        string $engine,
        int $doors,
        bool $airConditioning,
        bool $navigation,
        bool $automaticTransmission,
        bool $sunroof,
        string $color
    ) {
    }
}
```

Usage:

```php
$car = new Car(
    '2.0 TDI',
    5,
    true,
    true,
    false,
    false,
    'black'
);
```

---

## What's the Problem?

A few months later, someone reads this:

```php
$car = new Car(
    '2.0 TDI',
    5,
    true,
    false,
    true,
    false,
    'black'
);
```

What do these values mean?

```php
true,
false,
true,
false
```

It is difficult to tell at a glance.

Even worse:

```php
$car = new Car(
    '2.0 TDI',
    5,
    false,
    false,
    false,
    false,
    'black'
);
```

It is very easy to pass arguments in the wrong order.

Constructors like this are commonly known as **Telescoping Constructors**.

---

## The Idea Behind the Builder Pattern

Instead of writing:

```php
$car = new Car(
    '2.0 TDI',
    5,
    true,
    true,
    false,
    false,
    'black'
);
```

we write:

```php
$car = (new CarBuilder())
    ->setEngine('2.0 TDI')
    ->setDoors(5)
    ->setAirConditioning(true)
    ->setNavigation(true)
    ->setColor('black')
    ->build();
```

This is much easier to read.

It is immediately clear which properties are being configured.

---

## How Does the Builder Pattern Work?

A Builder typically consists of three parts.

### 1. Product

The object being created.

```text
Car
```

---

### 2. Builder

An object that gradually configures the product.

```text
CarBuilder
```

---

### 3. `build()`

A method that returns the fully constructed object.

---

## Step 1 – Product

```php
class Car
{
    public function __construct(
        public string $engine,
        public int $doors,
        public bool $airConditioning,
        public bool $navigation,
        public string $color
    ) {
    }
}
```

---

## Step 2 – Builder

```php
class CarBuilder
{
    private string $engine = '';
    private int $doors = 4;
    private bool $airConditioning = false;
    private bool $navigation = false;
    private string $color = 'white';

    public function setEngine(string $engine): self
    {
        $this->engine = $engine;

        return $this;
    }

    public function setDoors(int $doors): self
    {
        $this->doors = $doors;

        return $this;
    }

    public function setAirConditioning(bool $value): self
    {
        $this->airConditioning = $value;

        return $this;
    }

    public function setNavigation(bool $value): self
    {
        $this->navigation = $value;

        return $this;
    }

    public function setColor(string $color): self
    {
        $this->color = $color;

        return $this;
    }

    public function build(): Car
    {
        return new Car(
            $this->engine,
            $this->doors,
            $this->airConditioning,
            $this->navigation,
            $this->color
        );
    }
}
```

---

## Usage

```php
$car = (new CarBuilder())
    ->setEngine('2.0 TDI')
    ->setDoors(5)
    ->setAirConditioning(true)
    ->setNavigation(true)
    ->setColor('black')
    ->build();
```

Result:

```php
Car {
    engine: "2.0 TDI",
    doors: 5,
    airConditioning: true,
    navigation: true,
    color: "black"
}
```

---

## What Happens in Memory?

### Creating the Builder

```php
$builder = new CarBuilder();
```

Memory:

```text
builder
   ↓
CarBuilder
    engine = ''
    doors = 4
    airConditioning = false
    navigation = false
    color = white
```

---

### Calling

```php
$builder->setEngine('2.0 TDI');
```

Memory:

```text
builder
   ↓
CarBuilder
    engine = '2.0 TDI'
    doors = 4
    airConditioning = false
    navigation = false
    color = white
```

---

### Calling

```php
$builder->setColor('black');
```

Memory:

```text
builder
   ↓
CarBuilder
    engine = '2.0 TDI'
    doors = 4
    color = black
```

---

### Calling

```php
$car = $builder->build();
```

A new object is created:

```text
builder ---------> CarBuilder
car -------------> Car
```

The Builder's job is simply to collect the configuration and create the final object.

---

## Why Do We Return `self`?

```php
public function setEngine(string $engine): self
{
    $this->engine = $engine;

    return $this;
}
```

`$this` refers to the current Builder object.

Because we return it:

```php
return $this;
```

we can write:

```php
(new CarBuilder())
    ->setEngine('2.0 TDI')
    ->setDoors(5)
    ->setColor('black')
    ->build();
```

This technique is called **Method Chaining** (or a **Fluent Interface**).

Without returning `self`, we would need:

```php
$builder = new CarBuilder();

$builder->setEngine('2.0 TDI');
$builder->setDoors(5);
$builder->setColor('black');

$car = $builder->build();
```

---

## Real-World Example – SQL Query Builder

The Builder pattern is used in many frameworks.

For example:

```php
$query = (new QueryBuilder())
    ->select('name', 'email')
    ->from('users')
    ->where('active', '=', 1)
    ->orderBy('name')
    ->build();
```

Result:

```sql
SELECT name, email
FROM users
WHERE active = 1
ORDER BY name
```

The SQL query is built step by step.

---

## Real-World Example – HTTP Request

```php
$request = (new RequestBuilder())
    ->setMethod('POST')
    ->setUrl('/users')
    ->addHeader('Accept', 'application/json')
    ->setBody($data)
    ->build();
```

Again:

1. Create the Builder.
2. Configure the request step by step.
3. Call `build()`.
4. Receive the finished object.

---

## Builder in Laravel

Laravel's Query Builder is a classic example of the Builder pattern:

```php
$users = DB::table('users')
    ->where('active', 1)
    ->where('age', '>', 18)
    ->orderBy('name')
    ->get();
```

Each method:

```php
->where(...)
->orderBy(...)
```

adds another step to the query.

Only when:

```php
->get()
```

is called does Laravel execute the SQL query.

---

## Advantages

* ✅ Improves readability.
* ✅ Eliminates large constructors.
* ✅ Reduces mistakes caused by long parameter lists.
* ✅ Supports sensible default values.
* ✅ Builds complex objects step by step.
* ✅ Makes it easy to create different configurations of the same object.

---

## Disadvantages

* ❌ Requires additional classes and code.
* ❌ May be unnecessary for simple objects.
* ❌ Introduces another layer of abstraction.

---

## When Should You Use the Builder Pattern?

Good candidates include:

* `CarBuilder`
* `RequestBuilder`
* `QueryBuilder`
* `EmailBuilder`
* `DockerContainerBuilder`
* `ReportBuilder`

Avoid using a Builder for very simple objects, such as:

```php
class Point
{
    public function __construct(
        public int $x,
        public int $y
    ) {
    }
}
```

For objects with only two or three simple properties, a Builder usually adds unnecessary complexity.

---

## Rule to Remember

```text
Builder
   ↓
setSomething()
   ↓
setSomethingElse()
   ↓
setAnotherThing()
   ↓
build()
   ↓
Finished object
```

---

## The Essence of the Builder Pattern

> **The Builder pattern constructs complex objects step by step, making object creation more readable, flexible, and less error-prone.**

In other words:

* **Singleton says:** "There should be only one object."
* **Builder says:** "The object is complex, so build it step by step."
