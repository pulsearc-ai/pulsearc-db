# Benchmarks

Measured throughput and latency for pulsearc-db's public API. pulsearc-db is
an embedded, ordered key-value store; the numbers below cover the write,
read, and iteration paths against both storage backends.

## Methodology

- **Dataset:** 1,000 entries per sample — 12-byte keys, 64-byte values —
  with 10 samples per workload.
- **Backends:** every workload is run against both `Env` implementations:
  an **in-memory** backend and an **on-disk** (filesystem) backend.
- **Client:** single-threaded; setup (database open and load) is excluded
  from the timed section unless the workload name says otherwise.
- **Hardware:** MacBook Pro (Mac17,6) — Apple M5 Max, 18 cores
  (6 efficiency + 12 performance), 36 GB unified memory, running
  macOS 26.4.1. Throughput scales with CPU and, for the on-disk
  backend, storage; treat absolute figures as approximate (roughly
  ±10% run to run) and representative of relative cost between
  operations.

## Writes

| workload | in-memory | on-disk |
|---|--:|--:|
| `put` — point write | 6.3 M ops/s | 499 K ops/s |
| `put` with `sync` — durably flushed write | — | 31.3 K ops/s |
| `WriteBatch` — apply a 1,000-entry batch | 11.8 M ops/s | 7.4 M ops/s |
| `delete` — point delete | 6.1 M ops/s | 572 K ops/s |
| `put` + `compact_range` — write then materialize to SSTs | 439 K ops/s | 298 K ops/s |
| `WriteBatch` build + iterate (in-process, no database) | 24.8 M ops/s | — |

`sync` writes force every record to stable storage before returning, so
their throughput is bounded by the device's flush latency.

## Reads

| workload | in-memory | on-disk |
|---|--:|--:|
| `get` — key present | 8.1 M ops/s | 8.9 M ops/s |
| `get` — key absent | 20.8 M ops/s | 20.4 M ops/s |
| `get` — via a snapshot | 9.7 M ops/s | 9.6 M ops/s |
| `get` — after reopen (cold cache) | 1.7 M ops/s | 1.8 M ops/s |

A freshly reopened database starts with cold table and block caches; the
first reads pay one-time SST-open and block-decode costs, which is why
the cold-cache figure is lower than the steady-state `get`.

## Iteration

| workload | in-memory | on-disk |
|---|--:|--:|
| forward scan | 13.1 M entries/s | 20.4 M entries/s |
| reverse scan | 8.1 M entries/s | 7.9 M entries/s |
| iterator `seek` | 7.6 M ops/s | 8.2 M ops/s |
| forward scan — after reopen (cold cache) | 14.5 M entries/s | 7.9 M entries/s |

## Operation latency

Mean wall-clock time per call (10,000 calls per workload).

| operation | in-memory | on-disk |
|---|--:|--:|
| `put` | 0.29 µs | 1.96 µs |
| `delete` | 0.18 µs | 1.67 µs |
| `get` — key present | 0.10 µs | 0.10 µs |
| `get` — key absent | 0.05 µs | 0.05 µs |

## Maintenance operations

| operation | in-memory | on-disk |
|---|--:|--:|
| `get_property` | 8.1 M ops/s | 10.6 M ops/s |
| `get_approximate_sizes` | 2.0 M ops/s | 2.5 M ops/s |
| snapshot create + release | 30 M ops/s | 40 M ops/s |
| `compact_range` (mean time per call) | 0.20 ms | 0.64 ms |
| `force_flush` (mean time per call) | 0.26 ms | 0.68 ms |

## Notes

- The in-memory backend keeps all data in process memory and is intended
  for tests and ephemeral use; the on-disk backend is the durable,
  production path.
- `compact_range` and `force_flush` are whole-database maintenance
  operations; they are reported as time per call rather than throughput.
- Numbers are refreshed periodically and reflect the current `main`;
  last measured 2026-05-17.
