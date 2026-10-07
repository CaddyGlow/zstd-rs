# Fork validation

Base: KillingSpark/zstd-rs commit fe37617b53d611398303d3ffeee77a0a3a72bf31 (ruzstd 0.9.1).
Changes were ported from the local ms-compress adaptation of ruzstd 0.9.0.
Upstream license texts, attribution, corpus fixtures, and fuzz regressions remain.
Ring-buffer tests tied to the former raw allocation layout were replaced with
content checks against linear storage across wrap, growth, and reset operations.

Validated locally on Linux:

```sh
cargo fmt --all -- --check
cargo test --workspace --features ruzstd/dict_builder,ruzstd/fuzz_exports --locked
cargo test -p ruzstd --no-default-features --locked
cargo clippy --workspace --all-targets --features ruzstd/dict_builder,ruzstd/fuzz_exports --locked -- -D warnings
cargo clippy -p ruzstd --all-targets --no-default-features --locked -- -D warnings
cargo check -p ruzstd --lib --no-default-features --target thumbv7em-none-eabi --locked
```

The feature-enabled run passed 79 library tests, 4 retained-output integration
checks, 3 CLI tests, and 8 doctests. The no-default-features run passed 64 library
tests, the same 4 integration checks, and 7 doctests. These include the existing
upstream corpus, fuzz regression artifacts, and libzstd encoder interoperability.
XXH64 is checked against twox-hash across input lengths, chunk boundaries, and seeds.

The internal rustc-dep-of-std feature is excluded, as in upstream's feature-matrix
CI; it is intended for integration into libstd. The local generated Cargo.lock
is ignored under upstream's existing policy. Generate it before using --locked
in a fresh checkout. Continuous fuzzing, native Windows checks, and performance
benchmarks were not run.
