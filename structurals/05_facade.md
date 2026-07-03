# Facade Design Pattern

## Definition

**Facade** is a **structural design pattern** that provides a **simplified, unified interface** to a complex subsystem.

In other words, Facade hides the complexity of multiple classes and exposes a single, easy-to-use entry point to the client.

---

## Problem it solves

Imagine we are building a video converter.

To convert a video, we need to:

1. Load the file
2. Decode the video
3. Convert the format
4. Compress the video
5. Save the result

Without Facade:

```php id="pqitru"
$loader = new VideoLoader();
$video = $loader->load('movie.mp4');

$decoder = new VideoDecoder();
$data = $decoder->decode($video);

$converter = new FormatConverter();
$result = $converter->convert($data, 'avi');

$compressor = new VideoCompressor();
$compressed = $compressor->compress($result);

$saver = new VideoSaver();
$saver->save($compressed);
```

The client knows too much about the subsystem.

---

## Idea

We introduce a Facade:

```php id="p66o9w"
$converter = new VideoFacade();

$converter->convert('movie.mp4', 'avi');
```

The client uses a single class.

The Facade coordinates the whole subsystem.

---

## Structure

```text id="q5mqon"
Client
   |
Facade
   |
-------------------------
|     |     |     |     |
A     B     C     D     E
```

* **Client** – uses the system
* **Facade** – simplified interface
* **Subsystem classes** – do the actual work

---

## Example 1 – Home Theater

Without Facade:

```text id="v7gr9d"
TV
SoundSystem
DVDPlayer
Lights
Projector
```

To start a movie:

```php id="nj9f3e"
$lights->dim();
$projector->on();
$tv->on();
$sound->on();
$dvd->play();
```

Too many steps.

---

### Subsystem Classes

```php id="z1s0j6"
class TV
{
    public function on(): void
    {
        echo "TV on" . PHP_EOL;
    }
}

class SoundSystem
{
    public function on(): void
    {
        echo "Sound system on" . PHP_EOL;
    }
}

class DVDPlayer
{
    public function play(): void
    {
        echo "Playing movie" . PHP_EOL;
    }
}

class Lights
{
    public function dim(): void
    {
        echo "Lights dimmed" . PHP_EOL;
    }
}
```

---

### Facade

```php id="lvr9u4"
class HomeTheaterFacade
{
    public function __construct(
        private TV $tv,
        private SoundSystem $sound,
        private DVDPlayer $dvd,
        private Lights $lights
    ) {
    }

    public function watchMovie(): void
    {
        $this->lights->dim();
        $this->tv->on();
        $this->sound->on();
        $this->dvd->play();
    }
}
```

---

### Usage

```php id="h2ubnf"
$homeTheater = new HomeTheaterFacade(
    new TV(),
    new SoundSystem(),
    new DVDPlayer(),
    new Lights()
);

$homeTheater->watchMovie();
```

Output:

```text id="1jib9q"
Lights dimmed
TV on
Sound system on
Playing movie
```

---

## What we achieved

Instead of:

```php id="vkthng"
$lights->dim();
$tv->on();
$sound->on();
$dvd->play();
```

we now have:

```php id="n9y9n3"
$homeTheater->watchMovie();
```

A single method hides the complexity.

---

## Example 2 – Order System

An order requires:

* inventory check
* payment processing
* invoice creation
* sending email

---

### Subsystem

```php id="2f8l5u"
class InventoryService
{
    public function reserve(): void
    {
        echo "Items reserved" . PHP_EOL;
    }
}

class PaymentService
{
    public function pay(): void
    {
        echo "Payment successful" . PHP_EOL;
    }
}

class InvoiceService
{
    public function create(): void
    {
        echo "Invoice created" . PHP_EOL;
    }
}

class EmailService
{
    public function send(): void
    {
        echo "Email sent" . PHP_EOL;
    }
}
```

---

### Facade

```php id="v8c3po"
class OrderFacade
{
    public function __construct(
        private InventoryService $inventory,
        private PaymentService $payment,
        private InvoiceService $invoice,
        private EmailService $email
    ) {
    }

    public function placeOrder(): void
    {
        $this->inventory->reserve();
        $this->payment->pay();
        $this->invoice->create();
        $this->email->send();
    }
}
```

---

### Usage

```php id="kh7h3z"
$order = new OrderFacade(
    new InventoryService(),
    new PaymentService(),
    new InvoiceService(),
    new EmailService()
);

$order->placeOrder();
```

Output:

```text id="xxkqol"
Items reserved
Payment successful
Invoice created
Email sent
```

---

## Advantages

### 1. Simplifies a complex subsystem

Instead of:

```php id="0m3x4s"
$a->run();
$b->run();
$c->run();
$d->run();
```

we use:

```php id="z2wq2n"
$facade->execute();
```

---

### 2. Reduces coupling

Client depends on:

```php id="tt8o65"
OrderFacade
```

not on:

```php id="7v8sxy"
InventoryService
PaymentService
InvoiceService
EmailService
```

---

### 3. Hides implementation details

The client does not know:

* execution order
* number of services
* internal interactions

---

### 4. Follows SRP

Subsystem classes:

```php id="43pmby"
PaymentService
```

handle only payment.

Facade:

```php id="7ck2uv"
OrderFacade
```

coordinates the workflow.

---

### 5. Easier maintenance

If we add:

```php id="slq3ml"
class SmsService {}
```

we only update the Facade:

```php id="0gex0x"
OrderFacade
```

Client code stays unchanged.

---

## Disadvantages

### 1. Facade can become a God Object

```text id="4ld7ch"
ApplicationFacade
├── payment
├── orders
├── reports
├── users
├── invoices
├── emails
└── ...
```

---

### 2. It can hide useful subsystem features

Client sees only:

```php id="qz4d1c"
$facade->placeOrder();
```

but loses direct access to advanced functionality.

---

### 3. Adds another abstraction layer

```text id="q4fgwm"
Client
   ↓
Facade
   ↓
Subsystem
```

---

## When to use Facade?

✔ When the subsystem is complex
✔ When you want a simple API
✔ When you want to hide implementation details
✔ When you want to reduce dependencies

---

## When NOT to use it?

✘ When the subsystem is simple
✘ When clients need advanced subsystem features
✘ When Facade becomes too large

---

## Real-world example

### Car

Instead of:

```text id="0vbnhf"
Inject fuel
Start ignition
Start engine
Activate electronics
Release brake
```

We do:

```text id="f5jzc7"
Turn the key
```

The car acts as a Facade.

---

### E-commerce checkout

Customer clicks:

```text id="eud64g"
Place Order
```

Behind the scenes:

```text id="p7m0r7"
Reserve inventory
Process payment
Generate invoice
Send email
Create shipment
```

One call hides a full workflow.

---

## Facade vs Adapter

### Adapter

```text id="8y8x1g"
Convert Interface A → Interface B
```

Changes compatibility.

---

### Facade

```text id="7htc7g"
Complex subsystem → Simple interface
```

Simplifies usage.

---

## Facade vs Decorator

### Decorator

Adds behavior:

```text id="4z4j2e"
Coffee
 → MilkDecorator
 → SugarDecorator
```

---

### Facade

Hides complexity:

```text id="p0agqf"
Subsystems → Facade
```

---

## Facade vs Mediator

### Facade

Client → subsystem simplification

### Mediator

Components communicate through mediator

---

## Interview rule

**Facade = Provide a simple interface to a complex subsystem.**

Key idea:

```php id="2e6zlp"
class Facade
{
    public function operation(): void
    {
        $this->a->run();
        $this->b->run();
        $this->c->run();
    }
}
```

Facade:

```text
- does NOT change interfaces
- does NOT add behavior
- does NOT manage tree structures
```

Its only job:

```text
Hide complexity behind a simple API.
```
