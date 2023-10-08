# Development

To support platform dependent aspects of your application, such as input, audio and graphics, you will have to use User and Kernel functions exported by Vita modules.
These functions become available when linking against the appropriate stubs provided by [Vita SDK]. The descriptions of the exported data structures and functions can be found in [Vita SDK docs].

To use them in Rust, you must use [vitasdk-sys]. This crate uses [bindgen] to generate Rust bindings from C headers. The exported functions are grouped by their stubs and become available after enabling the corresponding feature in [vitasdk-sys]. The documentation can be found in [vitasdk-sys docs]. 


[Vita SDK]: https://vitasdk.org/
[Vita SDK docs]: https://docs.vitasdk.org/
[vitasdk-sys]: https://crates.io/crates/vitasdk-sys
[vitasdk-sys docs]: https://docs.rs/vitasdk-sys/0.3.2/vitasdk_sys/
[bindgen]: https://github.com/rust-lang/rust-bindgen
