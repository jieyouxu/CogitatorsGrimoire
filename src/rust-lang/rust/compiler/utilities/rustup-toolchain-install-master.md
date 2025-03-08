# rustup-toolchain-install-master

Link: <https://github.com/kennytm/rustup-toolchain-install-master>

## Summary

Allows you to install [rust-lang/rust] CI artifacts built in Merge CI or try jobs into `rustup`.

## Notes

- Note that try jobs notably may not produce the *full* toolchain (e.g. some try jobs that go through [`opt-dist`] don't built most tools by default).
- You can't remove `rustup-toolchain-install-master` toolchains by `rustup toolchain uninstall`, you need to use `rustup uninstall $full_hash` directly. 

[rust-lang/rust]: https://github.com/rust-lang/rust
[`opt-dist`]: https://github.com/rust-lang/rust/tree/master/src/tools/opt-dist
