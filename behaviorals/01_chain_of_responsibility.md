# Chain of Responsibility Design Pattern

## Definition

**Chain of Responsibility** is a **behavioral design pattern** that lets you pass a request along a chain of handlers. Upon receiving a request, each handler decides either to process the request or to pass it to the next handler in the chain.

---


## Problem it solves

Let’s assume we have an authentication system:

* User exists?
* Password correct?
* User active?
* User has permission?

Without CoR:

```php
function login(array $user): void
{
    if (!userExists($user)) {
        return;
    }

    if (!passwordCorrect($user)) {
        return;
    }

    if (!isActive($user)) {
        return;
    }

    if (!hasPermission($user)) {
        return;
    }

    echo 'Login successful';
}
```

Problems:

* one large method,
* hard to add new checks,
* hard to change order,
* violation of Open-Closed Principle.

---

## Idea

We create a chain:

```text
UserExistsHandler
        ↓
PasswordHandler
        ↓
ActiveHandler
        ↓
PermissionHandler
```

Each handler does one thing.

If it cannot handle the request:

```text
handle()
     ↓
next handler
```

---

## Structure

```text
Client
   |
Handler
   |
------------------------
|           |          |
HandlerA  HandlerB  HandlerC
```

or:

```text
HandlerA
    ↓
HandlerB
    ↓
HandlerC
```

---

## Roles

### Handler

Defines:

* request handling,
* reference to the next handler.

### ConcreteHandler

Implements concrete logic.

### Client

Builds the chain and sends the request.

---

## Example 1 – Authentication Pipeline

### Handler

```php
abstract class Handler
{
    protected ?Handler $next = null;

    public function setNext(
        Handler $handler
    ): Handler {
        $this->next = $handler;
        return $handler;
    }

    public function handle(
        array $request
    ): bool {
        if ($this->next !== null) {
            return $this->next->handle($request);
        }

        return true;
    }
}
```

---

### UserExistsHandler

```php
class UserExistsHandler extends Handler
{
    public function handle(array $request): bool
    {
        if (!$request['exists']) {
            echo 'User not found';
            return false;
        }

        return parent::handle($request);
    }
}
```

---

### PasswordHandler

```php
class PasswordHandler extends Handler
{
    public function handle(array $request): bool
    {
        if (!$request['password']) {
            echo 'Wrong password';
            return false;
        }

        return parent::handle($request);
    }
}
```

---

### ActiveHandler

```php
class ActiveHandler extends Handler
{
    public function handle(array $request): bool
    {
        if (!$request['active']) {
            echo 'Account inactive';
            return false;
        }

        return parent::handle($request);
    }
}
```

---

### Usage

```php
$userExists = new UserExistsHandler();
$password   = new PasswordHandler();
$active     = new ActiveHandler();

$userExists
    ->setNext($password)
    ->setNext($active);

$request = [
    'exists'   => true,
    'password' => true,
    'active'   => true,
];

$result = $userExists->handle($request);

var_dump($result);
```

Output:

```text
bool(true)
```

---

## What happens?

```text
Request
   ↓
UserExistsHandler
   ↓
PasswordHandler
   ↓
ActiveHandler
```

If:

```text
password = false
```

the chain stops:

```text
Request
   ↓
UserExistsHandler
   ↓
PasswordHandler
   ✖ stop
```

---

## Example 2 – Support System

```text
Basic Support
      ↓
Technical Support
      ↓
Manager
```

---

### Handler

```php
abstract class SupportHandler
{
    protected ?SupportHandler $next = null;

    public function setNext(
        SupportHandler $handler
    ): SupportHandler {
        $this->next = $handler;
        return $handler;
    }

    abstract public function handle(string $issue): void;
}
```

---

### Basic Support

```php
class BasicSupport extends SupportHandler
{
    public function handle(string $issue): void
    {
        if ($issue === 'password') {
            echo 'Password reset';
            return;
        }

        $this->next?->handle($issue);
    }
}
```

---

### Technical Support

```php
class TechnicalSupport extends SupportHandler
{
    public function handle(string $issue): void
    {
        if ($issue === 'server') {
            echo 'Server issue solved';
            return;
        }

        $this->next?->handle($issue);
    }
}
```

---

### Manager

```php
class Manager extends SupportHandler
{
    public function handle(string $issue): void
    {
        echo "Manager handles {$issue}";
    }
}
```

---

### Usage

```php
$basic     = new BasicSupport();
$technical = new TechnicalSupport();
$manager   = new Manager();

$basic
    ->setNext($technical)
    ->setNext($manager);

$basic->handle('server');
```

Output:

```text
Server issue solved
```

---

## Advantages

### 1. Loose Coupling

The client does not know who handles the request:

```php
$handler->handle($request);
```

---

### 2. SRP

Each handler has a single responsibility:

* PasswordHandler → password validation
* PermissionHandler → permission checks

---

### 3. OCP

Adding a new handler does not require modifying existing code.

---

### 4. Flexible order

```text
A → B → C
```

can become:

```text
C → A → B
```

by just changing the chain setup.

---

### 5. Dynamic pipeline

The chain can be built at runtime.

---

## Disadvantages

### 1. Request may not be handled

There is no guarantee that someone will process it.

---

### 2. Harder debugging

It may be unclear where the request stopped.

---

### 3. Long chains

Large chains can be hard to follow.

---

### 4. Performance overhead

Each handler is executed sequentially.

---

## When to use

* Middleware systems
* Validation pipelines
* Authentication flows
* Event processing
* Permission checks

---

## Laravel example

```php
Route::middleware([
    'auth',
    'verified',
    'throttle:60,1',
])->group(function () {
    //
});
```

Flow:

```text
Request → auth → verified → throttle → controller
```

---

## Chain vs Command

**Chain:** who handles the request
**Command:** a single encapsulated action

---

## Chain vs Mediator

**Chain:** request passes through a sequence
**Mediator:** all components communicate via a central object

---

## Chain vs Decorator

**Decorator:** adds behavior
**Chain:** decides whether to handle or pass further

---

## Interview rule

**Chain of Responsibility = Pass a request through handlers until one handles it or the chain ends.**

Core idea:

```text
Pipeline + Delegation + Conditional handling
```
