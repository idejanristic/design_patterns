# The Singleton Design Pattern

The **Singleton** is a **creational design pattern** that ensures **only one instance** of a class exists and provides a **global access point** to that instance.

---

## Definition

The **Singleton** design pattern:

1. Ensures that **only one instance (object)** of a class can be created.
2. Provides a **single, globally accessible point of access** to that instance.

Simply put:

> **Only one object of this class may exist, and every part of the application uses that same object.**

---

## Why Do We Need a Singleton?

Suppose we have a class that stores the application's configuration:

```php
$config1 = new Config();
$config2 = new Config();
$config3 = new Config();
```

This creates **three different objects**.

In some situations, this doesn't make sense. For example:

* application configuration,
* a logger,
* a cache manager,
* a database connection manager.

Instead, we want only one object:

```php
$config1 = Config::getInstance();
$config2 = Config::getInstance();
$config3 = Config::getInstance();
```

Now all three variables reference the **same object**.

---

## How Does the Singleton Work?

A Singleton consists of three essential parts.

### 1. A Private Static Property

This property stores the single instance of the class.

```php
private static ?Config $instance = null;
```

Why is it `static`?

Because the instance belongs to the **class**, not to any individual object.

If it were not static, we would first need to create an object:

```php
$config = new Config();
```

But creating objects directly is exactly what we want to prevent.

---

### 2. A Private Constructor

```php
private function __construct()
{
}
```

Why is it `private`?

Because it prevents other code from creating objects directly:

```php
$config = new Config();
```

PHP will throw an error:

```text
Call to private Config::__construct()
```

This means the object can only be created from inside the class itself.

---

### 3. The `getInstance()` Method

```php
public static function getInstance(): Config
{
    if (self::$instance === null) {
        self::$instance = new self();
    }

    return self::$instance;
}
```

### What happens?

The first call:

```php
Config::getInstance();
```

Since the instance doesn't exist yet:

```php
self::$instance === null
```

the following line is executed:

```php
self::$instance = new self();
```

A new object is created.

---

The second call:

```php
Config::getInstance();
```

The instance already exists, so:

```php
return self::$instance;
```

The same object is returned.

---

## Complete Example

```php
class Config
{
    private static ?Config $instance = null;

    private function __construct()
    {
    }

    public static function getInstance(): Config
    {
        if (self::$instance === null) {
            self::$instance = new self();
        }

        return self::$instance;
    }
}
```

Usage:

```php
$config1 = Config::getInstance();
$config2 = Config::getInstance();

var_dump($config1 === $config2);
```

Output:

```text
bool(true)
```

Both variables point to the same object.

---

## How It Looks in Memory

First call:

```php
$config1 = Config::getInstance();
```

Memory:

```text
Config::$instance ----> Config object #1
config1 -------------> Config object #1
```

---

Second call:

```php
$config2 = Config::getInstance();
```

Memory:

```text
Config::$instance ----> Config object #1
config1 -------------> Config object #1
config2 -------------> Config object #1
```

Both variables reference the same object.

---

## Real-World Example – Logger

```php
class Logger
{
    private static ?Logger $instance = null;

    private array $logs = [];

    private function __construct()
    {
    }

    public static function getInstance(): Logger
    {
        if (self::$instance === null) {
            self::$instance = new self();
        }

        return self::$instance;
    }

    public function log(string $message): void
    {
        $this->logs[] = $message;
    }

    public function getLogs(): array
    {
        return $this->logs;
    }
}
```

Usage:

```php
$logger1 = Logger::getInstance();
$logger2 = Logger::getInstance();

$logger1->log('User created');
$logger2->log('Email sent');

print_r($logger1->getLogs());
```

Output:

```php
Array
(
    [0] => User created
    [1] => Email sent
)
```

Why?

Because:

```php
$logger1 === $logger2
```

evaluates to:

```text
true
```

Both variables reference the same object.

---

## Why Do We Use `new self()`?

```php
self::$instance = new self();
```

Inside the `Config` class:

```php
new self()
```

is equivalent to:

```php
new Config()
```

So both statements create an instance of the `Config` class.

---

## Why Do We Use `self::$instance`?

Because:

```php
private static ?Config $instance = null;
```

is a **static property**.

Static members are accessed using:

```php
self::$instance
```

not:

```php
$this->instance
```

because `$this` exists only after an object has been created, and at this point there may not be any object yet.

---

## Building a Singleton Step by Step

### Step 1

```php
class Config
{
}
```

---

### Step 2

```php
class Config
{
    private static ?Config $instance = null;
}
```

---

### Step 3

```php
class Config
{
    private static ?Config $instance = null;

    private function __construct()
    {
    }
}
```

---

### Step 4

```php
class Config
{
    private static ?Config $instance = null;

    private function __construct()
    {
    }

    public static function getInstance(): Config
    {
        if (self::$instance === null) {
            self::$instance = new self();
        }

        return self::$instance;
    }
}
```

---

## Advantages

* ✅ Guarantees that only one instance exists.
* ✅ Reduces memory usage when only one object is needed.
* ✅ Provides centralized access to shared resources.
* ✅ Makes it easy to share state across different parts of the application.

---

## Disadvantages

* ❌ Introduces global state.
* ❌ Makes unit testing more difficult.
* ❌ Increases coupling between classes.
* ❌ Can hide dependencies, making the code harder to understand and maintain.

---

## When Should You Use the Singleton Pattern?

Good candidates include:

* `Config`
* `Logger`
* `CacheManager`
* `ConnectionManager`

Avoid using Singleton for classes such as:

* `User`
* `Product`
* `Order`
* `Post`
* `Comment`

because applications naturally require many instances of these objects.

---

## Rule to Remember

```text
private static $instance
        ↓
private constructor
        ↓
public static getInstance()
        ↓
if the instance doesn't exist → create it
if it already exists → return it
```

---

## The Essence of the Singleton Pattern

> **A Singleton ensures that a class has only one instance and provides a single global point of access to that instance.**

In other words:

> **One class → One instance → One shared object used throughout the application.**
