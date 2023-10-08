# Memory

Sony PlayStation Vita [SoC] has two memory dies:

- 512 MiB of LPDDR2 DRAM (which in allocations is further separated into MAIN, CDLG and PHYCONT)
- 128 MiB of CDRAM, usually used as video memory

## Budget

Not all of this memory would usually be available for your application though (see more about the [Memory Budget]).
By default your application has access only to 256 MiB of MAIN memory, 112 MiB of CDRAM, 26 MiB of PHYCONT and around 8 MiB of CDLG.

It is possible to increase the default MAIN memory budget to 365 MiB by passing `"-d ATTRIBUTE2=12` to `vita-mksfoex` tool of [Vita SDK] when building a `vpk`.
If you are using [cargo-vita] tool to build your project, this flag is set by default, and can be overriden by setting the following in your projects `Cargo.toml`:

```toml
[package.metadata.vita]
# ...
# Disables extended memory budget
vita_mksfoex_flags = []
# ...
```

## Stack

Stack is allocated on MAIN memory. The stack size for a main thread is 4 KiB.
To change the stack size of your main thread, export the following static variable:

```rust
#[used]
#[export_name = "sceUserMainThreadStackSize"]
pub static SCE_USER_MAIN_THREAD_STACK_SIZE: u32 = 1 * 1024 * 1024; // 1 MiB
```

When creating an new thread, you can set the stack size of it as usual:

```rust
pub fn main() {
    std::thread::Builder::new()
        .stack_size(1 * 1024 * 1024) // 1 MiB
        .spawn(move || todo!("work"))
        .expect("Unable to spawn thread")
        .join()
        .expect("Unable to join thread");
}
```

## Heap

The default [System allocator] in Rust will use [newlib heap]. This heap pre-allocates a memory block of a maximum heap size and never resizes. All allocation happen within this pre-allocated memory block in MAIN memory.

The default newlib heap size is 128 MiB. You can set a custom heap size by exporting the following static variable:

```rust
#[used]
#[export_name = "_newlib_heap_size_user"]
pub static _NEWLIB_HEAP_SIZE_USER: u32 = 256 * 1024 * 1024; // 256 MiB
```

## Considerations

Besides the newlib heap, if you are linking against native libraries or using syscalls of various modules provided by stubs, they may do custom allocations with [sceKernelAllocMemBlock] syscall.

Some of them may also be using sceLibc heap, which is another heap allocated on MAIN memory. If you experience OOM errors in such cases, you can resize the sceLibc heap size:


```rust
#[used]
#[export_name = "sceLibcHeapSize"]
pub static SCE_LIBC_HEAP_SIZE: u32 = 10 * 1024 * 1024; // 10 MiB
```

<div class="warning">

Keep in mind that stack, newlib heap and sceLibc heap are all located in MAIN memory, so their sum should not exceed the allowed memory budget.

</div>

## Manual allocation

In some cases (e.g when implementing a custom rasterizer with framebuffers), you may want to allocate memory outside of the heap or MAIN memory.

The low-level way to allocate memory on Sony PlayStation Vita is by using a [sceKernelAllocMemBlock] syscall

Here is an example of it may be used:

```rust
use vitasdk_sys::*;

pub struct Buffer {
    buf: *mut c_void,
    block_uid: SceUID,
    size: u32,
}

impl Buffer {
    fn new(size: u32) -> Buffer {
        let mut buf: *mut c_void = ::core::ptr::null_mut();

        // The minimal allocation unit is a memory page
        let unit = 0x40000 - 1;
        let size = (size + unit) & !unit;

        let block_uid = unsafe {
            let block_uid: SceUID = sceKernelAllocMemBlock(
                b"display\0".as_ptr() as *const c_char,
                SCE_KERNEL_MEMBLOCK_TYPE_USER_CDRAM_RW,
                size,
                ::core::ptr::null_mut(),
            );
            sceKernelGetMemBlockBase(block_uid, &mut buf);
            block_uid
        };

        Buffer {
            buf: buf as _,
            size,
            block_uid,
        }
    }
}

impl Drop for Buffer {
    fn drop(&mut self) {
        unsafe {
            sceKernelFreeMemBlock(self.block_uid);
        }
    }
}
```


[SoC]: https://wiki.henkaku.xyz/vita/Kermit
[Memory Budget]: https://wiki.henkaku.xyz/vita/Memory_budget
[Vita SDK]: https://vitasdk.org/
[cargo-vita]: https://github.com/vita-rust/cargo-vita
[sceKernelAllocMemBlock]: https://docs.vitasdk.org/group__SceSysmemUser.html#ga6f4fd5dc592c7e7a907225196160b597
[System allocator]: https://doc.rust-lang.org/stable/std/alloc/struct.System.html
[newlib heap]: https://github.com/vitasdk/newlib/blob/vita/newlib/libc/sys/vita/sbrk.c#L33
