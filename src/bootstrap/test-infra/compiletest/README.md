# compiletest

> Last reviewed: 2025-03-14

[`compiletest`] is the main test harness for rustc test suites.

## Key concepts

- [`compiletest`] is primarily responsible for orchestrating testing for the main compiler E2E test suites (i.e. *not* compiler unit tests) under `tests/`.
    - [`compiletest`] interfaces with the build system, `bootstrap`, in two directions:
        - (1) Bootstrap handles a CLI invocation like `./x test tests/ui` and builds suitable prerequisites, such as a suitably staged sysroot. Then, bootstrap builds [`compiletest`] as a binary, and then run the test suite through [`compiletest`] binary and passes information through CLI flags (and env vars).
        - (2) Bootstrap also relies on certain (JSON) output from compiletest re. test outcome.
    - Each of such test suite (e.g. `tests/ui`) have preset capabilities and behavior. What's possible for tests under one test suite is not necessarily so in another test suite.
- [`compiletest`] supports a diverse set of test needs -- snapshot tests, pattern-matching tests, behavioral tests, etc.

## Notable distinctions

- [`compiletest`] is *not* necessarily like other test steps managed by bootstrap. In fact, [`compiletest`] test suites receive special handling.
    - For instance, modulo test steps that maintain their own test infra (like tools that use oli's [`ui_test`]), the typical test step in bootstrap simply forwards some crates to `cargo test`.


[`compiletest`]: https://github.com/rust-lang/rust/tree/master/src/tools/compiletest
[`ui_test`]: https://github.com/oli-obk/ui_test
