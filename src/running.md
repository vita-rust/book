# Running

After you have built a `vpk` file, you have two options to run it:

- On a [hacked] Sony PlayStation Vita device
- On a [Vita3K] emulator

## Running on the real hardware

If you are going to test your application on physical hardware you first need to deliver and install your `vpk` on your Vita.

This can be either:

- Done manually with [VitaShell]
- Automated using [cargo-vita]. This will require you to have a number of additional tools installed (see [Getting Started]).


## Running on the emulator

After installing the emulator and setting it up correctly, installing and running your `vpk` is as simple as:

```sh
# Optionally remove the previous vpk with your title_id
vita3k -d RUST00001
# Installs the vpk and runs it
vita3k ./target/armv7-sony-vita-newlibeabihf/release/your_app.vpk

```

Keep in mind that an emulator is not a real device, and some programs that would work on real hardware won't work in an emulator, and vice versa.

[Getting Started]: ./getting_started.md#optional-tools
[hacked]: https://henkaku.xyz/
[Vita3K]: https://vita3k.org/
[VitaShell]: https://github.com/TheOfficialFloW/VitaShell
[cargo-vita]: https://github.com/vita-rust/cargo-vita
