# Composite Design Pattern

## Definition

**Composite** is a **structural design pattern** that lets you compose objects into **tree structures** and then work with those structures as if they were individual objects.

The pattern allows clients to treat both **individual objects (leaf nodes)** and **groups of objects (composites)** uniformly.

---

## Problem it solves

Let’s say we model a file system.

We have:

* File
* Directory

A directory can contain:

* File
* Directory

So we get:

```text
Root
├── file1.txt
├── file2.txt
└── Documents
    ├── cv.pdf
    └── Photos
        ├── image1.jpg
        └── image2.jpg
```

We want:

```php
$item->display();
```

to work for:

* a single file
* a whole directory

without:

```php
if ($item instanceof File) {
    ...
} elseif ($item instanceof Directory) {
    ...
}
```

---

## Idea

We define a common interface:

```php
interface FileSystemItem
{
    public function display(): void;
}
```

and implement it with:

```text
FileSystemItem
├── File
└── Directory
```

A directory contains a collection of:

```text
FileSystemItem
```

So:

```text
Directory
    |
    +---- File
    +---- File
    +---- Directory
                 |
                 +---- File
                 +---- File
```

This creates a recursive tree structure.

---

## Structure

```text
Component
├── Leaf
└── Composite
      |
      +---- Component
      +---- Component
      +---- Component
```

---

## Roles

### Component

Common interface for all objects.

### Leaf

Represents a single object.

### Composite

Represents a group of objects and contains children.

The client works only with:

```text
Component
```

and does not care whether it is:

```text
Leaf
or
Composite
```

---

## Example 1 – File System

### Component

```php
interface FileSystemItem
{
    public function display(): void;
}
```

---

### Leaf

```php
class File implements FileSystemItem
{
    public function __construct(
        private string $name
    ) {
    }

    public function display(): void
    {
        echo "File: {$this->name}" . PHP_EOL;
    }
}
```

---

### Composite

```php
class Directory implements FileSystemItem
{
    private array $items = [];

    public function __construct(
        private string $name
    ) {
    }

    public function add(FileSystemItem $item): void
    {
        $this->items[] = $item;
    }

    public function display(): void
    {
        echo "Directory: {$this->name}" . PHP_EOL;

        foreach ($this->items as $item) {
            $item->display();
        }
    }
}
```

---

### Usage

```php
$file1 = new File('readme.txt');
$file2 = new File('notes.txt');

$photos = new Directory('Photos');
$photos->add(new File('image1.jpg'));
$photos->add(new File('image2.jpg'));

$documents = new Directory('Documents');
$documents->add($file1);
$documents->add($file2);
$documents->add($photos);

$documents->display();
```

Output:

```text
Directory: Documents
File: readme.txt
File: notes.txt
Directory: Photos
File: image1.jpg
File: image2.jpg
```

---

## What we achieved

The client simply calls:

```php
$item->display();
```

It does not matter if:

```php
$item = new File(...);
```

or:

```php
$item = new Directory(...);
```

Both implement the same interface.

---

## Example 2 – Company Structure

```text
CEO
├── Manager A
│   ├── Developer 1
│   └── Developer 2
└── Manager B
    └── Developer 3
```

---

### Component

```php
interface Employee
{
    public function show(): void;
}
```

---

### Leaf

```php
class Developer implements Employee
{
    public function __construct(
        private string $name
    ) {
    }

    public function show(): void
    {
        echo "Developer: {$this->name}" . PHP_EOL;
    }
}
```

---

### Composite

```php
class Manager implements Employee
{
    private array $employees = [];

    public function __construct(
        private string $name
    ) {
    }

    public function add(Employee $employee): void
    {
        $this->employees[] = $employee;
    }

    public function show(): void
    {
        echo "Manager: {$this->name}" . PHP_EOL;

        foreach ($this->employees as $employee) {
            $employee->show();
        }
    }
}
```

---

### Usage

```php
$ceo = new Manager('CEO');

$managerA = new Manager('Manager A');
$managerB = new Manager('Manager B');

$managerA->add(new Developer('John'));
$managerA->add(new Developer('Alice'));

$managerB->add(new Developer('Bob'));

$ceo->add($managerA);
$ceo->add($managerB);

$ceo->show();
```

Output:

```text
Manager: CEO
Manager: Manager A
Developer: John
Developer: Alice
Manager: Manager B
Developer: Bob
```

---

## Recursive nature of Composite

The key idea:

```text
Composite
      |
      +---- Component
               |
               +---- Composite
               |
               +---- Leaf
```

A Composite can contain:

* Leaf
* Composite

because both implement the same interface.

This makes it ideal for modeling:

* file systems
* organizational structures
* GUI components
* menus
* product categories
* comment threads

---

## Advantages

### 1. Uniform treatment of single and grouped objects

```php
$item->display();
```

works for:

```text
File
Directory
```

without `if/else`.

---

### 2. Open-Closed Principle (OCP)

New types can be added:

```php
class Shortcut implements FileSystemItem
{
}
```

without changing existing code.

---

### 3. Simplifies client code

Without Composite:

```php
if ($item instanceof File) {
    ...
}

if ($item instanceof Directory) {
    ...
}
```

With Composite:

```php
$item->display();
```

---

### 4. Naturally models hierarchies

Great for:

```text
Folders
Menus
Organizations
Categories
GUI components
```

---

### 5. Uses polymorphism

Client depends on:

```php
FileSystemItem
```

not concrete classes.

---

## Disadvantages

### 1. Hard to restrict what a Composite can contain

Since everything implements:

```php
FileSystemItem
```

it is sometimes hard to enforce strict rules.

---

### 2. Can be overly generic

If there is no real tree structure, Composite may be unnecessary complexity.

---

### 3. Recursive debugging can be harder

Calls go:

```text
Directory
   ↓
Directory
   ↓
Directory
   ↓
File
```

making flow harder to follow.

---

### 4. Performance concerns

Very deep trees:

```text
10,000+ nodes
```

may cause performance issues with recursion.

---

## When to use Composite?

* ✅ When modeling hierarchical structures
* ✅ When treating individual and group objects uniformly
* ✅ When working with tree-like data
* ✅ When avoiding `if/else` type checks

---

## When to avoid it?

* ❌ No hierarchy exists
* ❌ No need to treat groups and single objects the same
* ❌ It adds unnecessary complexity
---

## Real-world example

### File Explorer

```text
Disk C
├── Program Files
│   ├── App1
│   └── App2
└── Users
    └── Documents
```

---

### HTML DOM

```text
html
├── head
└── body
    ├── div
    │   └── p
    └── footer
```

The DOM is one of the most famous examples of the Composite pattern.

---

## Difference between Composite and Decorator

### Composite

Focus:

```text
Part-Whole hierarchy
```

---

### Decorator

Focus:

```text
Adding responsibilities
```

---

## Difference between Composite and Iterator

### Composite

Represents structure:

```text
Tree
```

---

### Iterator

Traverses structure:

```text
Sequential access
```

They are often used together.

---

## Interview rule

**Composite = Compose objects into tree structures and treat individual and composite objects uniformly.**

Key concepts:

```text
Tree Structure
Part-Whole Relationship
Uniform Treatment
Recursion
```

Composite is used when:

```text
Single objects
and
Groups of objects
```

should behave the same from the client perspective, allowing the same operations regardless of depth in the hierarchy.
