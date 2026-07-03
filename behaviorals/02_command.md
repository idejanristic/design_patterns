# Command Design Pattern

## Definition

**Command** is a **behavioral design pattern** that turns a request into a standalone object containing all information about the request.

This transformation lets you parameterize methods with different requests, delay or queue a request's execution, and support undoable operations.

---

## Problem it solves

Let’s assume we have buttons in a GUI application.

Without Command pattern:

```php
class Button
{
    public function click(): void
    {
        // Save file
    }
}
```

Later we want:

* Save
* Print
* Export PDF
* Send Email

We end up with:

```php
if ($action === 'save') {
    ...
}

if ($action === 'print') {
    ...
}

if ($action === 'email') {
    ...
}
```

Problems:

* many `if/else`,
* Button knows too much business logic,
* hard to add new actions.

---

## Idea

We convert each action into an object.

Instead of:

```text
Button
    ↓
Save File
```

we get:

```text
Button
    ↓
Command
    ↓
Receiver
```

Button does not know what the command does.

It only knows:

```php
$command->execute();
```

---

## Structure

```text
Client
   |
Command
   |
---------------------
|                   |
ConcreteCommand   Invoker
        |
     Receiver
```

---

## Roles

### Command

Interface that defines:

```php
execute()
```

---

### ConcreteCommand

Implements a specific action.

---

### Receiver

Object that performs the real work.

---

### Invoker

Triggers the command.

---

### Client

Wires all objects together.

---

## Example 1 – Text Editor

### Receiver

```php
class TextEditor
{
    public function save(): void
    {
        echo 'File saved';
    }

    public function print(): void
    {
        echo 'File printed';
    }
}
```

---

### Command

```php
interface Command
{
    public function execute(): void;
}
```

---

### SaveCommand

```php
class SaveCommand implements Command
{
    public function __construct(
        private TextEditor $editor
    ) {
    }

    public function execute(): void
    {
        $this->editor->save();
    }
}
```

---

### PrintCommand

```php
class PrintCommand implements Command
{
    public function __construct(
        private TextEditor $editor
    ) {
    }

    public function execute(): void
    {
        $this->editor->print();
    }
}
```

---

### Invoker

```php
class Button
{
    public function __construct(
        private Command $command
    ) {
    }

    public function click(): void
    {
        $this->command->execute();
    }
}
```

---

### Usage

```php
$editor = new TextEditor();

$saveButton = new Button(
    new SaveCommand($editor)
);

$printButton = new Button(
    new PrintCommand($editor)
);

$saveButton->click();
echo PHP_EOL;
$printButton->click();
```

Output:

```text
File saved
File printed
```

---

## What did we get?

Button does not know:

* how files are saved,
* how printing works,
* who executes the action.

It only knows:

```php
$command->execute();
```

---

## Visualization

```text
Button
   ↓
SaveCommand
   ↓
TextEditor
```

or:

```text
Button
   ↓
PrintCommand
   ↓
TextEditor
```

---

## Example 2 – Queue System

### Receiver

```php
class EmailService
{
    public function send(string $email): void
    {
        echo "Email sent to {$email}";
    }
}
```

---

### Command

```php
class SendEmailCommand implements Command
{
    public function __construct(
        private EmailService $service,
        private string $email
    ) {
    }

    public function execute(): void
    {
        $this->service->send($this->email);
    }
}
```

---

### Queue

```php
class Queue
{
    private array $commands = [];

    public function push(Command $command): void
    {
        $this->commands[] = $command;
    }

    public function process(): void
    {
        foreach ($this->commands as $command) {
            $command->execute();
        }
    }
}
```

---

### Usage

```php
$service = new EmailService();

$queue = new Queue();

$queue->push(
    new SendEmailCommand($service, 'john@example.com')
);

$queue->push(
    new SendEmailCommand($service, 'alice@example.com')
);

$queue->process();
```

Output:

```text
Email sent to john@example.com
Email sent to alice@example.com
```

---

## Why is Command useful?

Because the request is an object:

```php
new SendEmailCommand(...)
```

we can:

* store it,
* queue it,
* log it,
* execute it later,
* retry it.

---

## Example 3 – Undo System

### Receiver

```php
class Light
{
    public function on(): void
    {
        echo 'Light ON';
    }

    public function off(): void
    {
        echo 'Light OFF';
    }
}
```

---

### Command

```php
interface UndoableCommand
{
    public function execute(): void;
    public function undo(): void;
}
```

---

### Concrete Command

```php
class LightOnCommand implements UndoableCommand
{
    public function __construct(
        private Light $light
    ) {
    }

    public function execute(): void
    {
        $this->light->on();
    }

    public function undo(): void
    {
        $this->light->off();
    }
}
```

---

### Usage

```php
$command = new LightOnCommand(new Light());

$command->execute();
echo PHP_EOL;
$command->undo();
```

Output:

```text
Light ON
Light OFF
```

---

## Advantages

### 1. Loose Coupling

Invoker depends on:

```php
Command
```

not on:

```php
TextEditor
EmailService
Printer
```

---

### 2. OCP

Adding:

```php
class ExportPdfCommand implements Command
{
}
```

does not require changing existing code.

---

### 3. SRP

Each command has one responsibility:

* SaveCommand → save
* PrintCommand → print
* EmailCommand → send email

---

### 4. Enables Queues

```php
$queue->push($command);
```

---

### 5. Enables Undo/Redo

```php
execute()
undo()
redo()
```

---

### 6. Enables Macro Commands

Multiple actions:

```text
Save → Print → Export
```

can be grouped into:

```text
DocumentWorkflowCommand
```

---

## Disadvantages

### 1. Many classes

You may end up with:

```text
SaveCommand
PrintCommand
ExportCommand
EmailCommand
```

---

### 2. Extra abstraction layer

```text
Button → Command → Receiver
```

instead of:

```text
Button → Receiver
```

---

### 3. Can be overengineering

For simple calls like:

```php
$service->save();
```

creating a command class may be unnecessary.

---

## When to use Command?

* Queue systems
* Undo/Redo
* GUI actions
* Job dispatching
* Event systems
* Scheduler systems
* Macro workflows

---

## When NOT to use it?

* Simple operations
* Small applications
* No need for deferred execution or undo

---

## Real-world example

### TV Remote

```text
Remote Button
      ↓
TurnOnCommand
      ↓
TV
```

or:

```text
Remote Button
      ↓
VolumeUpCommand
      ↓
TV
```

The button does not know how the TV works.

It only calls:

```php
$command->execute();
```

---

### Laravel Jobs

```php
SendEmailJob::dispatch($user);
```

Internally:

```text
Controller
      ↓
Job (Command)
      ↓
Queue
      ↓
EmailService
```

A Job is essentially a Command object.

---

## Difference: Command vs Chain of Responsibility

### Command

Focus:

```text
Encapsulate a request as an object
```

---

### Chain of Responsibility

Focus:

```text
Who handles the request?
```

---

## Difference: Command vs Strategy

### Strategy

Focus:

```text
How to perform an algorithm?
```

---

### Command

Focus:

```text
Represent an action/request
```

---

## Difference: Command vs Observer

### Observer

```text
One event → many listeners
```

---

### Command

```text
Invoker → one command → receiver
```

---

## Interview rule

**Command = Encapsulate a request as an object.**

Core idea:

```text
Request Object + Invoker + Receiver + Deferred Execution
```

Command is used when you want to:

```text
Turn requests into objects
Store them
Queue them
Undo them
Execute them later
```

without the invoker knowing how the action is actually performed.
