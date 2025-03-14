# compiletest binary

> Last reviewed: 2025-03-14

[`compiletest`] is a package with a dual library/binary organization:

1. The [`compiletest`] *binary* (`src/tools/compiletest/src/main.rs`) is a very thin wrapper around the [`compiletest`] *library*, which glues together the [`compiletest`] *library*'s provided functionality and provides a binary for bootstrap to build and run.
2. The [`compiletest`] *library* (`src/tools/compiletest/src/lib.rs`). This is the "meat" of [`compiletest`] and where all core functionality goes.

## Core responsibilities

The [`compiletest`] binary does a few things:

- Initialization of [`tracing`] (in particular, [`tracing_subscriber`]) infrastructure.
    - Note that this `tracing` infra is *independent* from bootstrap's `tracing` or rustc's `tracing`. For instance, this `tracing` infra is *unconditionally* built unlike bootstrap only building `tracing` bits if `BOOTSTRAP_TRACING=..` is set.
- Conditionally configure if color codes will be used for stdout/stderr.
    - FIXME: there's some strange `colored` configuration. And it's incomplete because it doesn't seem to also correctly influence stdout (only stderr)?
- Collect cli args and parse them into a config.
- For reasons I don't quite understand, there's two early configuration consistency checks that I feel don't belong in `compiletest` (but maybe in bootstrap):
    1. If no `html-tidy` was made available through bootstrap, and test mode is `Rustdoc`, then compiletest emits a warning that diffs will not be generated. But we don't fail. Is this even useful? Surely it should just fail? I should ask t-rustdoc contributors why this is a warning (especially considering it *should* fail in CI?).
    2. If profiler runtime was not made available through bootstrap (such as by actually setting `build.profiler = true`) to request bootstrap to build it, then we currently warn if test mode is `CoverageRun` but don't fail locally. That feels wrong? Or maybe this is so `./x test tests/` (default; no path filter) don't fail hard? I need to ask Zalathar.
- Logging config passed through to `compiletest` (for CI, mostly, so you can compare the `compiletest` config CI saw vs what you saw locally).
- Then hand off control to [`compiletest`] library via `run_tests`, passing the configuration through.


[`compiletest`]: https://github.com/rust-lang/rust/tree/master/src/tools/compiletest
[`tracing`]: https://docs.rs/tracing/latest/tracing/
[`tracing_subscriber`]: https://docs.rs/tracing-subscriber/0.3.19/tracing_subscriber/
