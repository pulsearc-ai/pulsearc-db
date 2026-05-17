# pulsearc-db

[![crates.io](https://img.shields.io/crates/v/pulsearc-db.svg)](https://crates.io/crates/pulsearc-db)
[![docs.rs](https://img.shields.io/docsrs/pulsearc-db)](https://docs.rs/pulsearc-db)
[![license](https://img.shields.io/crates/l/pulsearc-db.svg)](#license)

An embedded, ordered key-value store for Rust.

`pulsearc-db` is a pure-Rust LSM storage engine — no C/C++ dependency, no FFI.
It gives an application a fast, ordered, crash-safe key-value store that lives
entirely inside the process, with a small, explicit API.

## Features

- Embedded, ordered key-value store with `put` / `get` / `delete` and atomic
  batch writes
- Snapshots and snapshot-consistent iterators
- LSM storage: skiplist memtable, SSTable reader/writer, write-ahead log,
  manifest/version recovery, background flush and compaction
- Bloom filters and a sharded LRU block cache
- Pluggable `Env` (`StdEnv` for the filesystem, `MemEnv` for in-memory),
  `Comparator`, and `FilterPolicy`
- Database repair (`repair_db`) and teardown (`destroy_db`)
- Optional Snappy block compression
- No required dependencies; pure Rust

## Installation

```toml
[dependencies]
pulsearc-db = "0.1"
```

To enable the built-in Snappy compressor (pulls in the pure-Rust `snap` crate):

```toml
[dependencies]
pulsearc-db = { version = "0.1", features = ["snappy"] }
```

## Usage

```rust
use pulsearc_db::prelude::*;

fn main() -> Result<()> {
    let db = DBImpl::open(
        "/tmp/pulsearc-db-demo",
        StdEnv::default(),
        BytewiseComparator,
        Options::default(),
    )?;

    db.put(&WriteOptions::default(), b"key", b"value")?;

    let got = db.get(&ReadOptions::default(), b"key")?;
    assert_eq!(got.as_deref(), Some(&b"value"[..]));

    db.delete(&WriteOptions::default(), b"key")?;
    Ok(())
}
```

For an in-memory database, pass `MemEnv::new()` in place of `StdEnv`.

## Crate layout

Each public module is a focused layer of the storage engine — coding, blocks,
tables, the WAL, the memtable, version/manifest management, compaction; see the
crate-level docs on [docs.rs](https://docs.rs/pulsearc-db) for the full map. The
headline entry point is `db_impl::DBImpl`; most callers only need
`pulsearc_db::prelude`.

## License

The Rust code original to this crate is licensed under either of

- Apache License, Version 2.0 ([LICENSE-APACHE](LICENSE-APACHE))
- MIT license ([LICENSE-MIT](LICENSE-MIT))

at your option.

Portions of `pulsearc-db` are derived from [LevelDB](https://github.com/google/leveldb)
and remain covered by its BSD-3-Clause license, retained in
[LICENSE-BSD-3-CLAUSE](LICENSE-BSD-3-CLAUSE). The crate's overall SPDX
expression is `(MIT OR Apache-2.0) AND BSD-3-Clause`.

### Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in the work by you, as defined in the Apache-2.0 license, shall be
dual licensed as above, without any additional terms or conditions.
