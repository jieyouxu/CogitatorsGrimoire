# compiletest

[`compiletest`] is the main test harness for rustc test suites.

## Key concepts

- [`compiletest`] is primarily responsible for orchestrating testing for the main compiler E2E test suites (i.e. *not* compiler unit tests) under `tests/`.
    - [`compiletest`] interfaces with the build system, `bootstrap`, in two directions:
        - (1) Bootstrap handles a CLI invocation like `./x test tests/ui` and builds suitable prerequisites, such as a suitably staged sysroot. Then, bootstrap builds [`compiletest`] as a binary, and then run the test suite through [`compiletest`] binary and passes information through CLI flags (and env vars).
        - (2) Bootstrap also relies on certain (JSON) output from compiletest re. test outcome.
    - Each of such test suite (e.g. `tests/ui`) have preset capabilities and behavior. What's possible for tests under one test suite is not necessarily so in another test suite.

## Notable distinctions

- [`compiletest`] is *not* necessarily like other test steps managed by bootstrap. In fact, [`compiletest`] test suites receive special handling.


[`compiletest`]: https://github.com/rust-lang/rust/tree/master/src/tools/compiletest
