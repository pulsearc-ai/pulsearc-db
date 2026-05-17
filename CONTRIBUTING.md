# Contributing to pulsearc-db

Thanks for your interest in contributing. This document explains how to get
set up and what we expect from a change.

## Code of Conduct

This project follows the [Contributor Covenant](CODE_OF_CONDUCT.md). By
participating, you agree to uphold it.

## Getting started

You need a stable Rust toolchain (install via [rustup](https://rustup.rs)).

```sh
git clone https://github.com/pulsearc-ai/pulsearc-db
cd pulsearc-db
cargo build
cargo test
```

The crate itself has no required dependencies. Building the C++ parity
baseline additionally needs CMake and a C++ compiler, plus Boost on macOS
(`brew install boost`) — see the Development section of the [README](../../README.md).

## Before you open a pull request

Run the same checks CI runs:

```sh
cargo fmt --all -- --check
cargo clippy --all-targets --all-features -- -D warnings
cargo test --all-features
```

If you change `formats.schema.yml`, regenerate the committed output and
include it in your commit:

```sh
cargo run --manifest-path tooling/Cargo.toml --locked -p schema-codegen \
  --bin schema-codegen -- gen --schema formats.schema.yml \
  --out src/formats.generated.rs
```

CI fails if `src/formats.generated.rs` drifts from the schema.

Do not edit `src/formats.generated.rs` by hand — change the schema or the
generator in `tooling/` instead.

## Pull request guidelines

- Keep changes focused; one logical change per pull request.
- Add or update tests for any behavior change.
- Write clear commit messages explaining the *why*, not just the *what*.
- Make sure the full check suite above passes.

## Reporting bugs and requesting features

Open an issue on [GitHub](https://github.com/pulsearc-ai/pulsearc-db/issues).
Include the crate version, your platform, and a minimal reproduction where
possible. For security issues, do **not** open a public issue — follow
[SECURITY.md](SECURITY.md) instead.

## License

By contributing, you agree that your contributions will be dual licensed under
the MIT and Apache-2.0 licenses, as described in the [README](../../README.md),
without any additional terms or conditions.
