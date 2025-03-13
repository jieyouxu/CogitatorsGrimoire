# Josh sync

[rustc-dev-guide] is a [josh] subtree in [rust-lang/rust] at `src/doc/rustc-dev-guide`. Every week or so, we should perform a sync from [rust-lang/rust] and a sync back to [rust-lang/rust] in sequence.

1. [rust-lang/rust] to [rustc-dev-guide] direction (a.k.a. "rustc-pull"):
    - There is a bot that maintains a [Rustc pull update](https://github.com/rust-lang/rustc-dev-guide/pull/2251) PR, which can be used instead of manual rustc-pull **if** there are no merge conflicts. Otherwise, a manual rustc-pull will need to be performed to resolve the conflicts.

## Sync protocol

[rustc-dev-guide] has a [`josh-sync`] script to aid the sync workflow. To use it, it is required that you install `josh-proxy` (Linux-only) locally via

```bash
$ cargo +stable install josh-proxy --git https://github.com/josh-project/josh --tag r24.10.04
```

<div class="warning">
Older versions of `josh-proxy` may not round trip commits losslessly so it is important to install this exact version.
</div>

### rustc-pull

The "normal" invocation would be

```bash
$ cargo run --manifest-path josh-sync/Cargo.toml rustc-pull
```

**However**, my global git config is a bit of a mess, and in particular I set `fetch.prunetags = true` which breaks [`josh-sync`]'s git invocations for reasons I don't understand. As a safer version, I use a **minimal** git config

```toml
[user]
    name = ...
    email = ...
    signingKey = ...
```

then repoint git to use this instead of my actual global git config. The modified invocation looks like

```bash
$ GIT_CONFIG_GLOBAL=/path/to/minimal/gitconfig GIT_CONFIG_SYSTEM='' cargo +stable run --manifest-path josh-sync/Cargo.toml -- rustc-pull
```

You may have to fix merge conflicts as one does.

### rustc-push

TODO


[rustc-dev-guide]: https://github.com/rust-lang/rustc-dev-guide
[josh]: https://github.com/josh-project/josh
[rust-lang/rust]: https://github.com/rust-lang/rust
[`josh-sync`]: https://github.com/rust-lang/rustc-dev-guide/tree/master/josh-sync
