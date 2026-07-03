# Iterator Design Pattern

## Definition

**Iterator** is a **behavioral design pattern** that lets you traverse elements of a collection sequentially without exposing its underlying representation.

In other words, Iterator provides a way to access elements of a collection one by one without knowing how the collection stores those elements.

---

## Problem it solves

Suppose we have:

```php
class BookCollection
{
    private array $books = [];

    public function add(string $book): void
    {
        $this->books[] = $book;
    }
}
```

Client wants to iterate:

```php
foreach ($collection->books as $book) {
    echo $book;
}
```

Problem:

* `books` must be public,
* client depends on array structure,
* if implementation changes:

```text
array → linked list
array → database
array → file
```

all client code must change.

---

## Idea

Instead of:

```text
Client
   ↓
Collection internals
```

we use:

```text
Client
   ↓
Iterator
   ↓
Collection
```

Client only uses iterator:

```php
$iterator->next();
$iterator->current();
$iterator->valid();
```

and does not know how data is stored.

---

## Structure

```text
Client
   |
Iterator
   |
ConcreteIterator
   |
Aggregate
   |
ConcreteAggregate
```

---

## Roles

### Iterator

Defines traversal operations:

```php
current()
next()
valid()
rewind()
```

---

### ConcreteIterator

Implements iteration logic.

---

### Aggregate

Collection that can create an iterator.

---

### ConcreteAggregate

Concrete collection.

---

## Example 1 – Custom Iterator

### Iterator Interface

```php
interface IteratorInterface
{
    public function current(): mixed;

    public function next(): void;

    public function valid(): bool;

    public function rewind(): void;
}
```

---

### Collection

```php
class BookCollection
{
    private array $books = [];

    public function add(string $book): void
    {
        $this->books[] = $book;
    }

    public function getBooks(): array
    {
        return $this->books;
    }
}
```

---

### Concrete Iterator

```php
class BookIterator implements IteratorInterface
{
    private int $position = 0;

    public function __construct(
        private BookCollection $collection
    ) {}

    public function current(): mixed
    {
        return $this->collection->getBooks()[$this->position];
    }

    public function next(): void
    {
        $this->position++;
    }

    public function valid(): bool
    {
        return isset($this->collection->getBooks()[$this->position]);
    }

    public function rewind(): void
    {
        $this->position = 0;
    }
}
```

---

### Usage

```php
$books = new BookCollection();

$books->add('Clean Code');
$books->add('DDD');
$books->add('Refactoring');

$iterator = new BookIterator($books);

for (
    $iterator->rewind();
    $iterator->valid();
    $iterator->next()
) {
    echo $iterator->current() . PHP_EOL;
}
```

Output:

```text
Clean Code
DDD
Refactoring
```

---

## What we gained

Client does not know:

```text
array
linked list
database
file
```

It only uses:

```php
current()
next()
valid()
rewind()
```

---

## Visual

```text
BookCollection
      ↓
BookIterator
      ↓
Clean Code
DDD
Refactoring
```

---

## Example 2 – Reverse Iterator

We can have multiple iterators over the same collection.

```php
class ReverseBookIterator implements IteratorInterface
{
    private int $position;

    public function __construct(
        private BookCollection $collection
    ) {
        $this->position = count($collection->getBooks()) - 1;
    }

    public function current(): mixed
    {
        return $this->collection->getBooks()[$this->position];
    }

    public function next(): void
    {
        $this->position--;
    }

    public function valid(): bool
    {
        return $this->position >= 0;
    }

    public function rewind(): void
    {
        $this->position = count($this->collection->getBooks()) - 1;
    }
}
```

---

### Usage

```php
$iterator = new ReverseBookIterator($books);

for (
    $iterator->rewind();
    $iterator->valid();
    $iterator->next()
) {
    echo $iterator->current() . PHP_EOL;
}
```

Output:

```text
Refactoring
DDD
Clean Code
```

Same collection.

Different traversal strategy.

---

## PHP already has Iterator

PHP provides built-in:

```php
Iterator
```

Defines:

```php
current()
key()
next()
rewind()
valid()
```

---

## Laravel example

Laravel:

```php
User::cursor()
```

does not load all users at once.

Instead of:

```text
SELECT *
```

and millions of objects in memory,

we get:

```text
User
↓
User
↓
User
↓
...
```

one by one.

This is effectively:

```text
Iterator
```

---

## Advantages

### 1. Encapsulates collection structure

Client does not know:

```text
Array
Linked List
Database
File
```

---

### 2. Unified traversal interface

All collections expose:

```php
current()
next()
valid()
rewind()
```

---

### 3. Multiple traversal strategies

We can have:

```text
ForwardIterator
ReverseIterator
FilteredIterator
RandomIterator
```

over the same collection.

---

### 4. Single Responsibility Principle (SRP)

```text
Collection → stores data
Iterator   → traverses data
```

---

### 5. Open-Closed Principle (OCP)

Adding:

```php
class FilteredIterator
{
}
```

does not require modifying collection.

---

## Disadvantages

### 1. More classes

```text
Collection
ForwardIterator
ReverseIterator
FilteredIterator
```

---

### 2. Additional abstraction layer

```text
Client
   ↓
Iterator
   ↓
Collection
```

instead of:

```text
Client
   ↓
Array
```

---

### 3. Overkill for small collections

For:

```php
['A', 'B', 'C']
```

iterator may be unnecessary.

---

## When to use Iterator?

* Complex collections
* Multiple traversal strategies
* Hidden internal structure
* Lazy loading
* Stream processing

---

## When not to use it?

* Simple arrays
* Basic foreach usage is enough

---

## Real-world example

### Spotify playlist

```text
Playlist
   ↓
Iterator
   ↓
Song1
Song2
Song3
```

Different strategies:

* Normal order
* Reverse order
* Shuffle
* Favorites only

---

### File system

```text
Directory
   ↓
Iterator
   ↓
File1
File2
File3
```

Traversal:

* depth-first
* breadth-first
* recursive

---

## Iterator vs Composite

### Iterator

Focus:

```text
How to traverse?
```

---

### Composite

Focus:

```text
How to structure objects?
```

They are often used together.

---

## Iterator vs Command

### Iterator

Represents:

```text
Traversal algorithm
```

---

### Command

Represents:

```text
Action/request
```

---

## Iterator vs Generator

PHP:

```php
yield
```

Generators are specialized iterators that:

* produce values lazily
* use low memory
* implement iterator behavior automatically

---

## Interview rule

**Iterator = Provide a way to access elements sequentially without exposing internal structure.**

Key concepts:

```text
Traversal
Encapsulation
Sequential Access
Lazy Loading
Multiple Strategies
```

---

Iterator is used when we want to traverse a collection without exposing how it is stored internally.
