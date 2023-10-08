# Building

From here on you can write your code the same way you would write it for any other platform!


```rust
use std::time::Duration;

pub fn main() {
    std::thread::sleep(Duration::from_secs(10))
}
```



The usual final output for the Vita target is a `vpk` file.
A `vpk` file is essentially a zip archive with your applications binary, resources and a manifest for Vita bubbles.

A process of building a `vpk` involves a number of steps:

1. Building a usual eld with Rust std. The standard library can be compiled in rust using an unstable flag:
   ```sh
   cargo build -Z build-std=std,panic_abort --target=armv7-sony-vita-newlibeabihf --release
   ```
2. (Optional) Stripping unnecessary symbols from the binary to shrink it's size:
   ```sh
   arm-vita-eabi-strip -g your-binary.elf
   ```
3. Buildin a `velf` from your `elf`. This can be done using a tool from [Vita SDK] toolchain:
   ```sh
   vita-elf-create your-binary.elf your-binary.velf
   ```
4. Create an `eboot.bin` from the `velf`. This can be done using a tool from [Vita SDK] toolchain:
   ```sh
   vita-make-fself -s your-binary.velf eboot.bin
   ```
5. Pack the `eboot.bin`, assets and a manifest into a `vpk` file. This can be done using `vita-mksfoex` and `vita-pack-vpk` tools from [Vita SDK] toolchain.


All these steps can be by using `cargo-vita`:

```sh
# Build a elf
cargo vita build elf {{all arguments here are passed directly to cargo build}}
# Build a velf. Implicitly executes `cargo vita build elf`
cargo vita build velf {{all arguments here are passed directly to cargo build}}
# Build a eboot.bin. Implicitly executes `cargo vita build velf`
cargo vita build eboot {{all arguments here are passed directly to cargo build}}
# Build a eboot.bin. Implicitly executes `cargo vita build eboot`
cargo vita build vpk {{all arguments here are passed directly to cargo build}}
```

Just like the usual `cargo build` this tool will also build all workspace crates, unless a specific project is specified as a CLI flag.
Here are some more examples:

```sh
# Build all workspace crates with --release flag, and upload the resulting vpk files to ux0:/download/
cargo build vpk --upload --release
# Build std tests from examples
cargo vita build vpk --release --package vita-std-tests --tests
# Build a eboot for an already installed vpk, upload it to Vita, and run the application
cargo vita build eboot --update --run --release --package vita-example-http
# Build test suite named socket in a project that does not have [package.metadata.vita] section in Cargo.toml
cargo vita build --default-title-id=RUST00001 eboot --update --run --test socket --features all
```


[examples]: https://github.com/vita-rust/examples
[newlib]: https://github.com/vitasdk/newlib
[cargo-vita]: https://github.com/vita-rust/cargo-vita
[Vita SDK]: https://vitasdk.org/
