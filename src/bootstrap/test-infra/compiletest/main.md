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

## Immediate things I noticed that I want to improve

### Be very clear upfront that compiletest as-is is an internal-facing test support tool that has no plans to become a general purpose test harness

- Due to maintenance burden and technical requirements, there are *no* plans to make [`compiletest`] a general-purpose / general-audience-facing test harness that could be hosted on crates.io.
    - Manish has a [compiletest-rs] fork, but neither the compiler team nor bootstrap team has the bandwidth to maintain an ecosystem-facing version of [`compiletest`]. See below for reasons also why we *can't* make [`compiletest`] an ecosystem-facing test harness as-is.
- There are no stability guarantees *at all*. It intentionally does *not* conform to semver. It would not work with semver, unless every tiny change becomes a major version bump.
    - This is a *necessity*, because [`compiletest`] needs to respond rapidly to compiler changes and testing needs.
- [`compiletest`] is primarily responsible for supporting `rustc` test suites (but is also used by std and `rustdoc` in places).
- [`compiletest`] necessarily co-evolves with `rustc` *and* bootstrap.
    - This means that [`compiletest`] can and will consider itself specially privileged. It necessarily must.
    - [`compiletest`] can and will depend on and use perma-unstable, internal, testing-only compiler flags and behavior that no other ecosystem crates ordinarily on crates.io can nor should depend on, *because* it's used to test the compiler itself.
    - This in particular renders it not possible to make available as a general-purpose test harness as-is. Sure, it's MIT/Apache 2.0 dual-licenses, so parts of [`compiletest`] can be extracted and forked into eco-system crates, but they will *not* be maintained by bootstrap or compiler teams.

### Communication protocol between compiletest <-> bootstrap is unclear at best

- This looks quite problematic because output format for the [`compiletest`] binary is not first class.
- E.g. liberal uses of `eprintln!` warnings but this is not robust for broken pipes, output stream locking.
- No centralized diagnostics handling.
- There *is* no principled discipline/protocol for communication between compiletest/bootstrap. I think bootstrap tries to do something like look for JSON-shaped lines interleaved in non-JSON output...
- I think we should pull "shared" foundational types (e.g. for communication protocol) into something *like* `build_helpers`, but I argue it should be a separate crate to avoid making `build_helpers` a kitchen sink. Then, have bootstrap *and* `compiletest` rely on this as the source of truth.
    - For example: what test suites and test modes even exist in the first place? lolbinarycat tried to do this in <https://github.com/rust-lang/rust/pull/135653> but I disagreed with the *where*. At the time I thought maybe this should be in `compiletest` only but now I've changed my mind (see below).
    - I think we should rethink what `compiletest` *is*, i.e. not as a standalone tool, but instead a foundational part of bootstrap itself.
    - It's only a separate bootstrap tool to alleviate bootstrap build times.

> FIXME: overhaul communication protocol (or really the lack thereof) entirely, I would expect something like
>
> - compiletest *only* prints to stdout in JSON lines, e.g.
>
> ```json
> { "message-type": "diagnostics", "level": "warn", .. }
> { "message-type": "test-status", "test-status-kind": "test-suite-start", "test-suite-name": "ui", "test-suite-path": "tests/ui", "timestamp": "xxx", .. }
> { "message-type": "test-status", "test-status-kind": "test-start", "test-suite": "ui", "test-name": "tests/ui/diagnostics-width.rs", "revision": "", "timestamp": "xxx", .. }
> { "message-type": "test-status", "test-status-kind": "test-outcome", "test-suite": "ui", "test-name": "tests/ui/diagnostics-width.rs", "revision": "", "timestamp": "xxx", "test-outcome-kind": "fail", "test-stderr": "..", .. }
> ```
>
> - compiletest focuses primarily on the test running, *not* test output rendering.
> - Move test *rendering* responsibility to bootstrap.

### Cross-compile scenario needs to be considered first-class

Frankly, I'm not sure how [`compiletest`] handles cross-compile scenarios.

> TODO: how does [`compiletest`] support cross-compile tests?

> TODO: how does [`compiletest`], bootstrap and `remote-test-client` work together?

### There are no self-tests but compiletest needs to be rock solid

We are the tester. But [Who tests the tester?](https://github.com/rust-lang/rust/issues/47606)? A long-standing problem.

- [`compiletest`] was always kind of a sideline tooling thing where it had no real dedicated maintainer (let alone maintenance team).
- The implementation over the years mostly just *happened* but not much consideration was given for the overall picture since people changed [`compiletest`] to support their specific needs (reasonably and understandably so!).
- [`compiletest`] really needs significant amounts of self-test coverage:
    - Unit tests, snapshot tests, configuration tests, environment tests, you name it.
    - Also proper ways to perform E2E integration tests.


[`compiletest`]: https://github.com/rust-lang/rust/tree/master/src/tools/compiletest
[compiletest-rs]: https://github.com/Manishearth/compiletest-rs
[`tracing`]: https://docs.rs/tracing/latest/tracing/
[`tracing_subscriber`]: https://docs.rs/tracing-subscriber/0.3.19/tracing_subscriber/
