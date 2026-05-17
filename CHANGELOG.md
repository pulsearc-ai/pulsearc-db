# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.1.0]

Initial release.

### Added

- Pure-Rust embedded LSM storage engine, with no C/C++ dependency.
- Embedded ordered key-value store: `put`, `get`, `delete`, and atomic batch
  writes via `WriteBatch`.
- Snapshots and snapshot-consistent iterators.
- LSM storage path: skiplist memtable, SSTable reader/writer, write-ahead log,
  manifest/version recovery, and background flush and compaction.
- Bloom filters and a sharded LRU block cache.
- Pluggable `Env` (`StdEnv`, `MemEnv`), `Comparator`, and `FilterPolicy`.
- Database repair (`repair_db`) and teardown (`destroy_db`).
- Optional `snappy` feature for block compression.

[Unreleased]: https://github.com/pulsearc-ai/pulsearc-db/compare/v0.1.0...HEAD
[0.1.0]: https://github.com/pulsearc-ai/pulsearc-db/releases/tag/v0.1.0
