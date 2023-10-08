# Network

Sony PlayStation Vita has a support for IPv4 stack (IPv6 is not supported). Network API is exposed via usual POSIX socket API by [newlib].

Most of the existing popular libraries for should already either support Vita target, or it should be fairly trivial to port them by enabling it with [conditional checks], and disabling unsupported parts.

## Non-blocking sockets

In POSIX the usual way to make a socket non-blocking, or check if it is non-blocking is by using [fcntl] syscall.
Currently this syscall is not implemented in [newlib].

When porting network libraries for Vita, to change sockets non-blocking flag, or get this flags value, use `setsockopt` and `getsockopt` syscalls:

```rust
fn nonblocking(fd: libc::c_int) -> bool {
    let mut non_block: libc::c_int = 0;
    let mut len: libc::socklen_t = 0;
    unsafe {
        libc::getsockopt(
            fd,
            libc::SOL_SOCKET,
            libc::SO_NONBLOCK,
            &mut nonblock as *mut libc::c_int as _,
            &mut len as _,
        );
    }

    non_block != 0
}

fn set_nonblocking(fd: libc::c_int, non_block: bool) {
    let non_block = non_block as libc::c_int;
    unsafe {
        libc::setsockopt(
            fd,
            libc::SOL_SOCKET,
            libc::SO_NONBLOCK,
            &non_block as *const libc::c_int as _,
            std::mem::size_of::<libc::c_int>() as libc::socklen_t,
        );
    }
}
```

## HTTPS

In rust ecosystem when using TLS you usually can choose between two implementations - OpenSSL and [rustls].

Vita does not **natively** provide OpenSSL and instead has it's own API for TLS. But the usual installation of [Vita SDK] provides OpenSSL implementation which statically be linked to.

So in a nutshell, both choices should work on Vita.

There is an inconvenience with CA certificates though. Usually on a hacked Vita you would have [iTLS-Enso] installed for the latest CA certificates. These certificates are only available for the native TLS api. And the native TLS API provided by Vita does not have a way to export the CA certificates themselves.
The certificate file is located on the filesystem on `vs0:data/external/cert/CA_LIST.cer`, but the `vs0` partition is not available for the safe applications.

In practice this means the following:

- You can use [rustls] with [webpki-roots] crate to provide CA certificates. The certificates will be then bundled inside of your binary.
- Use either [rustls] with [rustls-native-certs] or [native-tls] crate for OpenSSL implementation. In both the scenarios your application will try to find CA certificated provided by the operating system.
  
  You will have to set a `SSL_CERT_FILE` environment variable in your code:

  ```rust
  fn main() {
      std::env::set_var("SSL_CERT_FILE", "vs0:data/external/cert/CA_LIST.cer");
      // ... your code
  }
  ```
  as well as make your application unsafe in order for it to have access to the `vs0` partition. To do that add the following to your `Cargo.toml`

  ```toml
  [package.metadata.vita]
  # ...
  # This disables safe mode (default -s flag) for reading OpenSSl certs
  vita_make_fself_flags = []
  ```


[newlib]: https://github.com/vitasdk/newlib
[conditional checks]: https://doc.rust-lang.org/rust-by-example/attribute/cfg.html
[fcntl]: https://man7.org/linux/man-pages/man2/fcntl.2.html
[rustls]: https://github.com/rustls/rustls
[Vita SDK]: https://vitasdk.org/
[iTLS-Enso]: https://github.com/SKGleba/iTLS-Enso
[webpki-roots]: https://github.com/rustls/webpki-roots
[rustls-native-certs]: https://github.com/rustls/rustls-native-certs
[native-tls]: https://github.com/sfackler/rust-native-tls
