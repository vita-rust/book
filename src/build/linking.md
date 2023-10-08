# Linking native dependencies

Currently [newlib] on Vita does not support dynamic linking.
Any native dependency you may want to use must be statically linked. This can be done in a number of ways.

You can declare them in your code:

```rust
#[link(name = "mathneon", kind = "static")]
extern "C" {}
```

Or you can define them in `build.rs`:

```rust

fn main() {
    println!("cargo:rustc-link-lib=mathneon");
}
```


[newlib]: https://github.com/vitasdk/newlib
