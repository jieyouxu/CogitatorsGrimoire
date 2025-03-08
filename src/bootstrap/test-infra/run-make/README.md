# run-make test infrastructure

## Overview

The `run-make` test infrastructure is supported by three pieces:

1. The [`run-make-support`] test support library.
    - Notably, [`run-make-support`] may act as an intermediary to allow recipes to use third-party crates via re-export.
2. [`compiletest`], the test harness for main rustc test suites.
3. And `rmake.rs` test recipes which are built by [`compiletest`] with [`run-make-support`] linked in.
    - `rmake.rs` **must** be compilable with latest stable rustc (i.e. no unstable features at all) to prevent stability/staging issues when run against stable/beta branches. This is also beneficial for custom codegen backends and whatnot to make it a less of a pain to specify a custom stage0 rustc/cargo.

[`run-make-support`]: https://github.com/rust-lang/rust/tree/master/src/tools/run-make-support
[`compiletest`]: ../compiletest/
