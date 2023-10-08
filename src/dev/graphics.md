# Graphics
There are multiple ways you can approach graphics programming on Vita.

# Approaches

## Native

You can use the native Vita modules for graphics programming. You can find the documentation for the API in [Vita SDK docs] and the bindings to the syscalls in [vitasdk-sys] crate.


## SDL2

The easiest way to start working with SDL2 in Rust would be using [rust-sdl2](https://github.com/Rust-SDL2/rust-sdl2) crate, which fully works on Vita without any additional work.

Keep in mind that graphics API provided by SDL2 is rudimentary and suitable mostly for simple 2D games. If you need something more complicated, you probably want to go the OpenGL route.

## OpenGL ES2

There exist two homebrew implementations for OpenGL. Keep in mind that probably neither of the solutions supports the full OpenGL ES2 spec, and may be limited by the capabilities of Vita GPU. 


### vitaGL

[VitaGL] provides OpenGL ES2 compatible API by using native GXM API internally and [vitaShaRK](https://github.com/Rinnegatamante/vitaShaRK) for shader compilation.

It is possible to use [vitaGL] together with `SDL2` as an abstraction layer for input, but the `SDL2` library provided by Vita SDK does not support [vitaGL], so for you will have to use this [SDL2 fork].

### PVR_PSP2

TODO [PVR_PSP2]

[Vita SDK docs]: https://docs.vitasdk.org/group__SceGxmUser.html
[vitasdk-sys]: https://github.com/vita-rust/vitasdk-sys/
[rust-sdl2]: https://github.com/Rust-SDL2/rust-sdl2
[vitaGL]: https://github.com/Rinnegatamante/vitaGL
[SDL2 fork]: https://github.com/Northfear/SDL
[PVR_PSP2]: https://github.com/GrapheneCt/PVR_PSP2
