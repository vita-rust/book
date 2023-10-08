# Getting started

## Requirements

Vita is a [tier 3 target] with std available when compiled from source by the user.

To build Rust application for Sony PlayStation Vita you need the following installed:

- Rust nightly (since building std is an unstable feature)
- [Vita SDK] (for std and tools to build a runnable artifact)
- [cargo-vita] (to simplify the process of building runnable artifacts)


[Cargo-vita] uses [Vita SDK] toolchain for compilation, and requires you to set `VITASDK` environment variable to your [Vita SDK] installation path.

Also, you will have to add the following section to your projects `Cargo.toml`:

```toml
[package.metadata.vita]
# A unique identifier for your project. 9 chars, alphanumeric.
title_id = "RUSTAPP01"
# A title that will be shown on a bubble. Optional, will take the crate name as the default
title_name = "My application"
# Optional. A path to static files relative to the project.
assets = "static"
# Optional, this is the default
build_std = "std,panic_unwind"
# Optional, this is the default
vita_strip_flags = ["-g"]
# Optional, this is the default
vita_make_fself_flags = ["-s"]
# Optional, this is the default
vita_mksfoex_flags = ["-d", "ATTRIBUTE2=12"]
```

## Optional tools

Optionally you may want to have a number of tools installed to simplify the development and debugging process.

- [vitacompanion] for `--upload`, `--update` and `--run` flags of [cargo-vita]
- [PrincessLog] is required for `cargo vita logs`
- [vita-parse-core] for `cargo vita coredump parse`


[tier 3 target]: https://doc.rust-lang.org/nightly/rustc/platform-support/armv7-sony-vita-newlibeabihf.html
[Vita SDK]: https://vitasdk.org/
[cargo-vita]: https://github.com/vita-rust/cargo-vita
[docker image]: https://github.com/vita-rust/docker
[vitacompanion]: https://github.com/devnoname120/vitacompanion
[PrincessLog]: https://github.com/CelesteBlue-dev/PSVita-RE-tools/tree/master/PrincessLog/build
[vita-parse-core]: https://github.com/xyzz/vita-parse-core
