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
