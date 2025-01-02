---
title: Notes on Rust
cascade:
  type: docs

---

These are just a few notes to remember features of Rust I rarely use.

## Concurrency

### Channels/message passing

[Book 16.1](https://doc.rust-lang.org/book/ch16-02-message-passing.html)

```rust
use std::thread;
use std::sync;

fn main() {
    let messages = vec!("Hello", "darkness", "my", "old", "friend");

    // Create the channel.
    let (rx,tx) = sync::mpsc::channel();

    // Spawn threads.
    let producer = thread::spawn(move || for msg in messages { rx.send(msg).unwrap(); });
    let consumer = thread::spawn(move || for msg in tx.iter() { println!("Got {msg}");});

    // Join threads
    producer.join().unwrap();
    consumer.join().unwrap();
}
```

### Shared state

```rust
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    let counter = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    for _ in 0..10 {
        let counter = Arc::clone(&counter);
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap();

            *num += 1;
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }

    println!("Result: {}", *counter.lock().unwrap());
}
```

## Memory

### Smart pointers

 - `Box<T>`: stores a `T` on the heap.
 - `Rc<T>`: runtime reference counting, read-only access.
 - `Arc<T>`: `Rc` but `Send`.  Runtime reference counting, read-only access, atomic (thread-safe)
 - `RefCell<T>`: interior mutability.

## Generics

### `impl Trait` in parameter position

These two forms are almost equivalent:

``` rust
trait Trait {}

// generic type parameter
nnnnfn with_generic_type<T: Trait>(arg: T) {
}

// impl Trait in argument position
fn with_impl_trait(arg: impl Trait) {
}
```

That is, `impl Trait` in argument position is syntactic sugar for a
generic type parameter like `<T: Trait>`, except that the type is
anonymous and doesn’t appear in the GenericParams list.

### `impl Trait` in return position

Useful to return closures, since the type of closures is unique and
cannot be written:

```rust
fn returns_closure() -> impl Fn(i32) -> i32 {
    |x| x + 1
}
```

### Dynamic dispatch with `dyn`

``` rust
use std::fmt::Display;

fn main() {
    let mut test: Vec<Box<dyn Display>> = vec!();;
    test.push(Box::new("Hello"));
    test.push(Box::new(33));
    test.push(Box::new(0.07));
    test.push(Box::new(true));
    test.into_iter().for_each(|who| println!("Hello, {who}!"));
}
```

### const Generics

Stupid example:

``` rust
fn take<const N: usize>(s: &str) -> &str {
    &s[0..N]
}

fn main() {

    let func = take::<7>;
    let test = "Gandalf the Grey";
    println!("See? {}", func(test));

}
```

## Types

### Associated constants

Types and traits can have associated constants.  See the
[reference](https://doc.rust-lang.org/reference/items/associated-items.html#associated-constants)
