# Rust

Rust is a modern systems programming language focusing on safety, speed, and concurrency. It accomplishes these goals by being memory safe without using garbage collection.

## Hello world

```rust
fn main() {
  // println! is a macro that prints text to the console
  println!("Hello world!");
}
```

Build and run

```shell
rustc hello.rs
./hello
```

## Comments

- Regular comments which are ignored by the compiler:

```rust
// Line comments which go to the end of the line.
/* Block comments which go to the closing delimiter. */
```

- Doc comments which are parsed into HTML library documentation:

```rust
/// Generate library docs for the following item.
//! Generate library docs for the enclosing item.
```

## Formatted print

```rust
fn main() {
    // In general, the `{}` will be automatically replaced with any
    // arguments. These will be stringified.
    println!("{} days", 31);
    println!("{} days", 31i64);

    println!("{0}, this is {1}. {1}, this is {0}", "Alice", "Bob");

    println!("{subject} {verb} {object}",
             object="the lazy dog",
             subject="the quick brown fox",
             verb="jumps over");

    // Special formatting can be specified after a `:`.
    println!("{} of {:b} people know binary, the other half doesn't", 1, 2);

    // You can pad numbers with extra zeroes. This will output "000001".
    println!("{number:0>width$}", number=1, width=6);

    #[allow(dead_code)]
    struct Structure(i32);

    // However, custom types such as this structure require more complicated
    // handling. This will not work.
    println!("This struct `{}` won't print...", Structure(3));
    
    // This structure cannot be printed either with `fmt::Display` or
    // with `fmt::Debug`.
    struct UnPrintable(i32);

    // The `derive` attribute automatically creates the implementation
    // required to make this `struct` printable with `fmt::Debug`.
    #[derive(Debug)]
    struct DebugPrintable(i32);
}
```
