# Proxy Design Pattern

## Definition

**Proxy** is a **structural design pattern** that provides a **substitute or placeholder** for another object and controls access to it.

In other words, a Proxy sits between the client and the real object and can perform additional actions before or after delegating requests to the real object.

---

## Problem it solves

Suppose we have an expensive object:

```php id="img1"
class Image
{
    public function display(): void
    {
        echo 'Loading image from disk...' . PHP_EOL;
        echo 'Displaying image';
    }
}
```

Loading the image is expensive:

* disk I/O
* memory allocation
* processing

We don’t want to load it until it is really needed.

---

## Idea

Instead of:

```text id="p1"
Client
   ↓
Real Object
```

we introduce a Proxy:

```text id="p2"
Client
   ↓
Proxy
   ↓
Real Object
```

The Proxy decides:

* when to create the object
* whether access is allowed
* whether results should be cached
* whether to log calls

---

## Structure

```text id="p3"
Client
   |
Subject
   |
------------------
|                |
RealSubject     Proxy
```

---

## Roles

### Subject

Common interface for RealSubject and Proxy.

### RealSubject

The real object that does the work.

### Proxy

Controls access to RealSubject.

### Client

Works with Subject interface only.

---

## Example 1 – Virtual Proxy (Lazy Loading)

### Subject

```php id="p4"
interface Image
{
    public function display(): void;
}
```

---

### Real Subject

```php id="p5"
class RealImage implements Image
{
    public function __construct(
        private string $filename
    ) {
        echo "Loading {$this->filename}" . PHP_EOL;
    }

    public function display(): void
    {
        echo "Displaying {$this->filename}";
    }
}
```

---

### Proxy

```php id="p6"
class ImageProxy implements Image
{
    private ?RealImage $image = null;

    public function __construct(
        private string $filename
    ) {
    }

    public function display(): void
    {
        if ($this->image === null) {
            $this->image = new RealImage($this->filename);
        }

        $this->image->display();
    }
}
```

---

### Usage

```php id="p7"
$image = new ImageProxy('photo.jpg');

echo 'Application started';
echo PHP_EOL;

$image->display();
```

Output:

```text id="p8"
Application started
Loading photo.jpg
Displaying photo.jpg
```

---

## Example 2 – Protection Proxy

### Subject

```php id="p9"
interface UserService
{
    public function deleteUser(int $id): void;
}
```

---

### Real Subject

```php id="p10"
class RealUserService implements UserService
{
    public function deleteUser(int $id): void
    {
        echo "User {$id} deleted";
    }
}
```

---

### Proxy

```php id="p11"
class UserServiceProxy implements UserService
{
    public function __construct(
        private RealUserService $service,
        private bool $isAdmin
    ) {
    }

    public function deleteUser(int $id): void
    {
        if (!$this->isAdmin) {
            throw new Exception('Access denied.');
        }

        $this->service->deleteUser($id);
    }
}
```

---

### Usage

```php id="p12"
$service = new UserServiceProxy(
    new RealUserService(),
    true
);

$service->deleteUser(5);
```

Output:

```text id="p13"
User 5 deleted
```

---

## Example 3 – Logging Proxy

### Subject

```php id="p14"
interface PaymentService
{
    public function pay(int $amount): void;
}
```

---

### Real Subject

```php id="p15"
class StripeService implements PaymentService
{
    public function pay(int $amount): void
    {
        echo "Paid {$amount}";
    }
}
```

---

### Proxy

```php id="p16"
class LoggingPaymentProxy implements PaymentService
{
    public function __construct(
        private PaymentService $service
    ) {
    }

    public function pay(int $amount): void
    {
        echo "Logging payment..." . PHP_EOL;

        $this->service->pay($amount);
    }
}
```

---

### Usage

```php id="p17"
$service = new LoggingPaymentProxy(
    new StripeService()
);

$service->pay(100);
```

Output:

```text id="p18"
Logging payment...
Paid 100
```

---

## Types of Proxy

### 1. Virtual Proxy

Lazy initialization of expensive objects.

---

### 2. Protection Proxy

Controls access based on permissions.

---

### 3. Remote Proxy

Represents remote objects (API, microservices).

---

### 4. Caching Proxy

Caches expensive results.

---

### 5. Logging Proxy

Adds logging, metrics, tracing.

---

## Advantages

### 1. Access control

Proxy controls access to real object.

---

### 2. Lazy loading

Object is created only when needed.

---

### 3. Additional behavior

* logging
* caching
* authorization
* monitoring

---

### 4. Open-Closed Principle

New proxies can be added without modifying existing code.

---

### 5. Single Responsibility Principle

* RealSubject → business logic
* Proxy → infrastructure concerns

---

## Disadvantages

### 1. More classes

Each service often has a proxy counterpart.

---

### 2. Extra indirection

Call chain:

```text id="p19"
Client → Proxy → RealObject
```

---

### 3. Small performance overhead

Extra method layer.

---

### 4. Harder debugging

Hard to see where logic is executed.

---

## When to use Proxy?

* Lazy loading
* Access control
* Logging / monitoring
* Caching
* Remote services

---

## When NOT to use it?

* simple systems
* no access control needed
* unnecessary abstraction

---

## Real-world example

### Credit Card

```text id="p20"
Client
  ↓
Credit Card Proxy
  ↓
Bank Account
```

---

### ORM Lazy Loading

```php id="p21"
$user = User::find(1);
```

```php id="p22"
$user->posts;
```

Posts are loaded only when accessed → Virtual Proxy behavior.

---

## Difference: Proxy vs Decorator

### Proxy

Focus:

```text id="p23"
Control access
```

Examples:

* caching
* authorization
* lazy loading

---

### Decorator

Focus:

```text id="p24"
Add behavior
```

Examples:

* logging
* encryption
* compression

---

## Difference: Proxy vs Adapter

### Proxy

Controls access to object.

---

### Adapter

Changes interface.

---

## Difference: Proxy vs Facade

### Facade

Simplifies subsystem.

---

### Proxy

Controls access to single object.

---

## Interview definition

**Proxy is a structural design pattern that provides a surrogate or placeholder for another object to control access to it.**

---

## Key idea

```text id="p25"
Client talks to Proxy instead of Real Object
Proxy decides when/how/if Real Object is used
```
