# Building in a CI pipeline

If you want to build your `vpk` in a CI pipeline, you can use a `ghcr.io/vita-rust/vitasdk-rs` [docker image].
This image is based on alpine, and contains [Vita SDK], Rust nightly and [cargo-vita].

The image is rebuilt automatically by a Github Actions pipeline with the latest dependencies on a weekly basis.

[cargo-vita]: https://github.com/vita-rust/cargo-vita
[docker image]: https://github.com/vita-rust/docker
[Vita SDK]: https://vitasdk.org/
