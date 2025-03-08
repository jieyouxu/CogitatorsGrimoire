# rustup toolchain manager TUI

Sometimes with [`cargo-bisect-rustc`] and [`rustup-toolchain-install-master`] you can end up with stray nightlies that are never cleaned up but are otherwise useless. It's a bit of a pain doing `rustup toolchain remove xxx` manually; would be nice if there was a TUI manager for `rustup` toolchains to allow you to quickly cleanup unused toolchains.

## Notes

- [`rustup-toolchain-install-master`] installs toolchains that `rustup` doesn't know how to remove by `rustup toolchain remove`, instead they need to be uninstalled via `rustup uninstall $hash` directly[^direct-uninstall].

[^direct-uninstall]: see [notes on `rustup-toolchain-install-master`](../../rust-lang/rust/compiler/utilities/rustup-toolchain-install-master.md).

[`cargo-bisect-rustc`]: https://github.com/rust-lang/cargo-bisect-rustc
[`rustup-toolchain-install-master`]: https://github.com/kennytm/rustup-toolchain-install-master
