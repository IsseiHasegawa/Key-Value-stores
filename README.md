# Junior-semi-data

This repository contains a C++ distributed key-value store implementation and Python-based integration tests.

## Basic Information

- **Project type**: Distributed key-value store (C++), with Python integration/correctness tests.
- **Main server binary**: `build/kvnode`
- **Workload binary**: `build/kv_workload`
- **Main source directory**: `src/`
- **Test directory**: `tests/`
- **Build output directory**: `build/`

### Core Features

- TCP-based JSON message protocol (`KV_SET`, `KV_GET`, and control messages).
- Optional replication modes (`none`, `sync`, `async`).
- Failure detection with fixed-threshold and phi-accrual options.
- Structured JSONL logging for node events and test analysis.

## Getting Started (After Cloning)

If you only want the fastest path, follow steps 1 -> 4 in order.  
If something fails, check the "Notes" under each step.

### 1) Clone the repository

```bash
git clone <your-repo-url>
cd data
```

**What this does**
- Downloads the source code to your local machine.
- Moves you into the project root, where all build and test commands should be run.

**Notes**
- Replace `<your-repo-url>` with the actual Git URL.
- If your folder name is not `data`, use the directory name that `git clone` created.

### 2) Build the binaries

```bash
mkdir -p build
cd build
cmake ..
make
cd ..
```

**What this does**
- `cmake ..` generates native build files from the CMake configuration.
- `make` compiles the C++ source code into executable binaries.

After building, you should have:
- `build/kvnode`
- `build/kv_workload`

**Notes**
- If `cmake` is not found, install CMake first.
- If build errors appear, ensure you have a C++ compiler (for example, `clang++` on macOS).

### 3) Run a node (example)

```bash
./build/kvnode \
  --id node0 \
  --port 19100 \
  --log_path /tmp/node0.jsonl \
  --run_id local_test
```

**What this does**
- Starts one KV server node on port `19100`.
- Writes structured runtime logs to `/tmp/node0.jsonl`.
- Tags logs with `run_id` so you can separate runs later.

In another terminal, you can send test requests (one line JSON each), for example:

```bash
printf '{"type":"KV_SET","key":"hello","value":"world","req_id":"r1"}\n' | nc 127.0.0.1 19100
printf '{"type":"KV_GET","key":"hello","req_id":"r2"}\n' | nc 127.0.0.1 19100
```

Expected behavior:
- The first command returns a `KV_SET_RESP` with `ok: true`.
- The second command returns a `KV_GET_RESP` with `value: "world"`.

**Notes**
- Keep the node process running while you send requests.
- If `nc` is unavailable, you can use any TCP client that sends newline-terminated JSON.

### 4) Run tests

Run all comprehensive tests:

```bash
python3 tests/test_all.py
```

Other test suites:

```bash
python3 tests/test_kvstore.py
python3 tests/test_implementation.py
```

**What each suite is for**
- `test_all.py`: broad end-to-end and feature coverage.
- `test_kvstore.py`: integration behavior (networking, replication, failure handling).
- `test_implementation.py`: deeper correctness checks for protocol and internal behavior.

**Notes**
- Tests expect built binaries in `build/`.
- If a port conflict happens, stop other running nodes/tests and retry.
