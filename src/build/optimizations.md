# Optimizations

To reduce the size of the resulting artifacts, prefer using `release` builds, and add the following to your root `Cargo.toml`:

```toml
[profile.release]
panic = 'abort'
lto = true
opt-level = 3
```
