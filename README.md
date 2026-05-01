# Key-Value-stores

This is the implementation repository for a distributed key-value store written in C++.  
Build `kvnode` and `kv_workload` here, then run experiments from the companion [Research-data](https://github.com/IsseiHasegawa/Research-data) repository (Python commands run inside `Research-data/new-kv-store-data/`).

## Related repositories and folder names

| GitHub repository | Example clone folder | Role |
|-------------------|----------------------|------|
| [Key-Value-stores](https://github.com/IsseiHasegawa/Key-Value-stores) (this repo) | `Key-Value-stores` or `kvstore-impl` | Build `kvnode` and `kv_workload`; pass this directory as `--impl-root` to the experiment scripts. |
| [Research-data](https://github.com/IsseiHasegawa/Research-data) | `Research-data` | Experiment orchestration, metrics, and plots. Scripts live under `Research-data/new-kv-store-data/`. |

The Research-data README uses the name **`kvstore-impl`** for the implementation checkout; that is the same codebase as this repository, regardless of the directory name you choose when cloning.

## Basic Information

- Role: Implementation repository (provides server/workload binaries)
- Language: C++17
- Build: CMake 3.10+ and a C++17 toolchain (GCC 7+, Clang 5+, or Apple Clang)
- Main binaries:
  - `build/kvnode` (node process)
  - `build/kv_workload` (workload generator)
- Core features:
  - TCP + JSON protocol (`KV_SET`, `KV_GET`, control messages)
  - Replication modes (`none`, `sync`, `async`)
  - Failure detection (`fixed`, `phi`)
  - Structured JSONL logging

## Folder Structure

```text
Key-Value-stores/
├── CMakeLists.txt
├── README.md
├── src/
│   ├── main.cpp                  # kvnode entry point
│   ├── node/                     # node control and KV store
│   ├── replication/              # replication logic
│   ├── failure_detector/         # fixed-threshold / Phi Accrual detector
│   ├── common/                   # networking, messages, logger
│   └── client/workload.cpp       # kv_workload
├── tests/
│   ├── test_all.py
│   ├── test_kvstore.py
│   └── test_implementation.py
└── build/                        # generated at build time
```

## From Clone to Build

```bash
git clone https://github.com/IsseiHasegawa/Key-Value-stores.git Key-Value-stores
cd Key-Value-stores
mkdir -p build
cd build
cmake ..
make
```

After building, these binaries should exist:

- `build/kvnode`
- `build/kv_workload`

## Minimal Smoke Test

```bash
cd Key-Value-stores
./build/kvnode \
  --id node0 \
  --port 19100 \
  --log_path /tmp/node0.jsonl \
  --run_id local_test
```

In another terminal:

```bash
printf '{"type":"KV_SET","key":"hello","value":"world","req_id":"r1"}\n' | nc 127.0.0.1 19100
printf '{"type":"KV_GET","key":"hello","req_id":"r2"}\n' | nc 127.0.0.1 19100
```

## Tests

```bash
python3 tests/test_all.py
python3 tests/test_kvstore.py
python3 tests/test_implementation.py
```

## How This Connects to Data Collection

This repository alone does not run the full experiment analysis pipeline.  
Data collection and aggregation are run from [Research-data](https://github.com/IsseiHasegawa/Research-data): after cloning that repo, `cd` into **`new-kv-store-data/`**, then run `scripts/run_experiments.py` with `--impl-root` pointing at this implementation directory (see the Research-data README for exact paths). That script consumes the binaries built here.
