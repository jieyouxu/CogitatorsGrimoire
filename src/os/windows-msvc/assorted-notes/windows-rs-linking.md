# Cannot open input file windows.0.52.0.lib

## Symptom

If you see a MSVC link.exe error like

```
error: linking with `link.exe` failed: exit code: 1181
  [..]
  = note: LINK : fatal error LNK1181: cannot open input file 'windows.0.52.0.lib'
```

## Possible solutions

Double-check if dependencies transitively depend on `windows-sys`, that may transitively attempt to link to `ntdll`s.

Experiment with setting `windows_raw_dylib` cfg which would prevent the lib crates from being included.

## Extra remarks

- [`raw-dylib` feature][raw-dylib] is stabilized but `windows` crates (incl.`windows-sys`) will need to wait until it bumps its MSRV to be able to use that.

## References

- <https://github.com/microsoft/windows-rs/issues/2640>
- <https://github.com/rust-lang/rust/issues/58713>

[raw-dylib]: https://github.com/rust-lang/rust/issues/58713
