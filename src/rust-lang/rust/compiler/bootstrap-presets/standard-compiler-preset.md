# Standard compiler preset

Standard compiler preset.

## Config

```toml
profile = "compiler"
change-id = 999999

[build]
#profiler = true

[llvm]
download-ci-llvm = true
#assertions = true

[rust]
codegen-backends = ["llvm"]
deny-warnings = true
debug = false
debuginfo-level = 1
debug-logging = true
debug-assertions = true
debug-assertions-std = true
```

## Notes

- No `profiler` builds since I don't usually work on those.
- Force CI LLVM download, I don't want to build it locally.
    - CI LLVM may require a fairly new (if not newest) MSVC toolchain on Windows MSVC since MSVC CI jobs typically get bumped to use latest Visual Studio toolchains (this is when `rustc_llvm` doesn't build and throws a bunch of errors related to not finding C++ STL symbols).
- CI LLVM does *not* currently have LLVM assertions enabled (those might be available in alt builds but not for all Tier 1 targets).
- `debug-assertions{-std}` may cause test failures that are not present in certain CI jobs or cause unexpected test passes in certain CI jobs.
    - `//@ {needs,ignore}-{rustc,std}-debug-assertions` compiletest directives may be useful if that's important.
