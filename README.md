# DreamTeam Miner

**dtminer 1.4.0** — a CPU miner for **Midstate Walk**, the proof of work of Midstate (MDS) from
block 371,500 and of Midwimble (MW) from block 37,300. It mines on MDS + MW pools that pay both
coins from the same work.

**Developer fee: 2%.**

> **Confidential source: never release it. We release only closed binaries** (see [NOTICE.md](NOTICE.md)).
> Private repository: the source is shared with the DreamTeam collaborators to work on it; it is not
> open source (see [LICENSE](LICENSE)).

- Written in C++17 for Linux (g++) and Windows (MSVC), x86-64, with a hand-written assembly kernel
  for the Walk steps.
- The Walk code is a clean-room implementation written from the textual definition of Walk v1,
  not from the official GPL-3.0 source: read [CLEAN-ROOM.md](CLEAN-ROOM.md) before touching it.
- No external libraries: sockets, JSON, the stratum client and the terminal dashboard are part of
  the program. BLAKE3 comes from the official BLAKE3 C library (`third_party/blake3`).
- A live dashboard in the terminal (hashrate per thread, pool, shares) that updates in place, with
  the plain scrolling log a `--no-ui` away for services and log files.

## Download

Compiled binaries are in the **[Releases](../../releases)** (Windows x64 and Linux x86-64, with
SHA-256 checksums).

## Quick start

DreamTeam MDS + MW pool: `mds-mid.dreamteamcrypto.xyz:3336` (PPLNS; the IP is `62.84.176.247:3336`).

```sh
dtminer --pool mds-mid.dreamteamcrypto.xyz:3336 --mds YOUR_MDS_ADDRESS --mw YOUR_MW_ADDRESS --worker rig1
```

- `YOUR_MDS_ADDRESS`: Midstate address, 64 or 72 hex characters.
- `YOUR_MW_ADDRESS`: Midwimble address, `mw1...` (bech32m; the checksum is verified).
- Before the Midstate Walk fork (block 371,500) the pool still sends BLAKE3 jobs: the miner stays
  connected, does not hash, and says it is waiting for a Walk job.
- Stop it with **Ctrl+C**: it closes the connection and prints a summary.

## Supported CPUs

- x86-64. The Linux build targets x86-64-v2 (SSE4.2: Intel Nehalem / AMD Bulldozer or newer); the
  Windows build needs only SSE2. The Linux release binary is static: no glibc version needed (give
  the pool as an IP address).
- AES-NI is used when the CPU has it; otherwise a portable AES path is used. Same results.
- On CPUs with **AES-NI and BMI2** (Intel Haswell / AMD Excavator and newer) the Walk steps run in
  the assembly kernel, about 10% faster than the C++ loop; the startup line says
  `aes-ni + assembly step kernel`. Elsewhere (and with `--no-asm`) the C++ loop runs. Some virtual
  machines hide BMI2 from the guest: there the C++ loop runs.
- Memory: 256 KiB of scratchpad per thread; Walk is bound by the L2 cache latency.
- Windows with more than 64 logical CPUs (several processor groups) is supported: every group is used.

## Self-test

Every start, before connecting, the miner computes the official known answers (golden vectors) for
the network it mines, on the AES path it will use and on the portable path. If any answer is wrong
it prints `SELF-TEST FAILED` and refuses to mine (exit code 3). `dtminer --self-test` checks all
six vectors (production and test network) on every AES path the CPU supports.

## Options

| Option | Meaning |
|---|---|
| `--pool HOST:PORT` | pool address; `stratum+tcp://HOST:PORT` is accepted too |
| `--mds ADDRESS` | your Midstate payout address: 64 or 72 hex characters |
| `--mw ADDRESS` | your Midwimble payout address, starting with `mw1` |
| `--worker NAME` | letters, digits, `_` and `-`, up to 48 characters (default: the computer name) |
| `--threads N` | hashing threads (default: every logical CPU, see `--placement`) |
| `--placement MODE` | `auto` (default), `cores` (one thread per physical core) or `all` (every logical CPU). Threads go one per physical core first, across all processor groups, then on the second logical CPU of each core |
| `--affinity MODE` | `core` (default: each thread stays on its core), `cpu` (one logical CPU per thread) or `none` |
| `--large-pages on\|off` | scratchpads on 2 MiB pages when the system allows it (default `on`) |
| `--aes MODE` | `auto` (default), `aesni` or `portable` |
| `--no-asm` | do not use the assembly step kernel |
| `--no-ui` | plain scrolling log instead of the live dashboard. The plain log is also used when stdout is not a terminal (piped to a file, run by a service) or when `WALK_NO_UI` is set; `NO_COLOR` keeps the dashboard but without colours |
| `--test-network` | private test network: test Walk parameters, Walk from height 20 |
| `--self-test` | check the built-in known answers and exit |
| `--benchmark [S]` | attempts per second on 1 thread and on N threads, S seconds each (default 10) |
| `--help` | list of options |

### Large pages

With two threads per core, scratchpads on 2 MiB pages are measurably faster (+13 to +19% on a
64-core Threadripper). At start the miner prints what it obtained, e.g.
`Large pages: 128 of 128 scratchpads on 2 MiB pages`, or why not.

- **Windows** needs the *Lock pages in memory* right for your account (Local Security Policy →
  User Rights Assignment; sign out and in again). The miner only enables the right for itself.
- **Linux** uses transparent huge pages (`/sys/kernel/mm/transparent_hugepage/enabled` set to
  `always` or `madvise`); the miner checks how many it really got.

## What the screen shows

In a terminal the miner draws a dashboard: the panels are updated in place once a second and the
log rolls in the box below them, so nothing scrolls away while you watch.

```
            ╔╦╗╦═╗╔═╗╔═╗╔╦╗╔╦╗╔═╗╔═╗╔╦╗  ╔╦╗╦╔╗╔╔═╗╦═╗
             ║║╠╦╝║╣ ╠═╣║║║ ║ ║╣ ╠═╣║║║  ║║║║║║║║╣ ╠╦╝
            ═╩╝╩╚═╚═╝╩ ╩╩ ╩ ╩ ╚═╝╩ ╩╩ ╩  ╩ ╩╩╝╚╝╚═╝╩╚═
┌─dtminer v1.4.0 - [Linux]───────────────────────────────────┐┌─Uptime───────┐
│ Algorithm: midstate-walk  AES: aes-ni + assembly step ker… ││ 0d 01:02:14  │
│ Threads: 8 (cores/core)  Pages: 8 of 8 scratchpads on 2 M… ││ since 21:28  │
│ CPU: 16 cores / 32 logical CPUs, L2 512 KiB per core       ││              │
└────────────────────────────────────────────────────────────┘└──────────────┘
┌─Pools───────────────────────────────────────────────────────────────────────┐
│ Algorithm      Pool                Worker  Diff   Latency  TLS  State       │
│ midstate-walk  62.84.176.247:3336  rig1    1.02M  41ms     no   mining      │
└─────────────────────────────────────────────────────────────────────────────┘
┌─[midstate-walk]─────────────────────────────────────────────────────────────┐
│ #     Hashrate        Share    Load                                         │
│ 0     297.0 att/s     12.6%    ████████████████████████████                 │
│ 1     294.5 att/s     12.5%    ███████████████████████████▊                 │
│ Total 2.36 katt/s  avg 2.34 katt/s  peak 2.41 katt/s  acc 12 rej 0 stale 1  │
│ ▃▄▅▆▇█▇▆▅▄▃▄▅▆▇█                                                            │
└─────────────────────────────────────────────────────────────────────────────┘
┌─Log─────────────────────────────────────────────────────────────────────────┐
│ [22:30:01] New Walk job: height 371640, job 9124, share target 0000ffff...  │
│ [22:30:07] Share accepted (12 accepted in total).                           │
└─────────────────────────────────────────────────────────────────────────────┘
```

- **Pools**: the connection. `Diff` is the share difficulty of the job being hashed, `Latency` the
  last round-trip to the pool, `State` is `mining`, `dev fee` while the developer fee is running,
  or why the connection is down. The columns are sized to what they hold, so a long pool hostname
  is shown in full.
- **[midstate-walk]**: `att/s` is Walk attempts per second. One row per thread with its share of
  the total and a bar against the fastest thread — a thread that is throttling or stuck shows a
  visibly short bar. Above 12 threads the rows become a compact grid of per-thread figures.
  `Total` carries the rate now, the average since start, the peak, and the share counters:
  `acc` / `rej` are the pool's answers and `stale` counts shares for a job replaced before they
  could be sent, or answered "Stale job" (normal in small numbers). The last line is the hashrate
  of the recent past, scaled between its own low and high.
- **Log**: the newest lines, tinted by what they say. Older lines roll out of the window.

The dashboard fits itself to the terminal and redraws on resize: on a short window the CPU row,
then the Pools panel, then the wordmark give way so the log keeps its space. Colours are the
DreamTeam palette, 24-bit where the terminal supports it and 256-colour where it does not; box
drawing falls back to ASCII outside a UTF-8 locale.

### Plain log

With `--no-ui`, when stdout is not a terminal (piped to a file, run by a service) or when
`WALK_NO_UI` is set, the miner prints the scrolling log it always did, with a status line every 30
seconds:

```
[2026-11-06 22:30:00] 33102.5 att/s (per thread min 240.1, max 297.0) | shares accepted 12, rejected 0, stale 1 | height 371640, job 9124 | connected to POOL_HOST:PORT as rig1
```

`NO_COLOR` is different: it keeps the dashboard and only drops the colours.

If the connection drops, the miner reconnects after 5 s, then 10, 20, 40, up to 60 s.

## Benchmarks

For Linux **Ting / Daler (DLR) CUDA solo mining**, build with `make ting` and
select `dtminer --algo ting`. See [Ting mining and tuning](docs/TING.md).

dtminer 1.3.2 (released as walk-miner) on an AMD Threadripper PRO 5995WX (Zen 3, 64 cores / 128 threads, Windows 11),
2 MiB pages, measured while the PC was in use by other programs:

| Threads | Attempts/s |
|---:|---:|
| 128 | **~40,000** |
| 1 | 507 – 552 |

1.3.x and 1.4.0 have the same hashing engine as 1.2.0.

All results, the CPUs we want numbers for, and the comparison with the official miner:
[benchmarks/](benchmarks/README.md). How to run them: [BENCHMARK.md](BENCHMARK.md).

Where the time goes, what each optimization bought, what was tried and rejected, and what is left:
[docs/optimizations/](docs/optimizations/README.md).

## Build

### Linux (g++ 9 or newer)

```sh
make dtminer                             # -> build/dtminer
make dtminer MINERLDFLAGS="-static -s"   # fully static, as in the releases
make test                                # known-answer, cross-check and fee-schedule tests
```

Only `src/walk_aesni.cpp` is compiled with `-maes`; its code runs only after the CPU reports AES-NI.
The assembly step kernel (`src/walk_kernel_sysv.S` for Linux, `src/walk_kernel_win64.asm` for
Windows) is generated by `python tools/gen_walk_kernel.py`; it runs only after the CPU reports
AES-NI and BMI2. `make WALK_NO_ASM_KERNEL=1` builds without it.

### Windows (Visual Studio 2022 Build Tools, x64)

```bat
tools\build-windows.bat windows-x64
```

Output in `build\windows-x64\` (or in `%WALK_BUILD_ROOT%\windows-x64` if that variable is set):
`dtminer.exe` plus the test programs (`walk_test.exe`, `fee_test.exe`, `walk_bench.exe`,
`walk_profile.exe`). `/MT` links the C++ runtime statically, so the exe runs without the Visual C++
redistributable.

### Before committing a change to the Walk code

- `walk_test` and `dtminer --self-test` must pass on every AES path.
- `tests/old_vs_new.py` must show identical hashes between the new build and the previous release.
- Re-check against the official code with the fake pool (`testing/`): `WRONG HASHES: none`.

## Protocol

The miner speaks the stratum dialect of the MDS + MW pool ([`spec/PROTOCOL.md`](spec/PROTOCOL.md)):
one JSON object per line over TCP; `mining.subscribe`, `mining.authorize` with
`[MDS address, worker, MW address]`, `mining.notify` with 4 parameters (BLAKE3-chain job, not mined)
or 5 parameters (the fifth is the Midstate height; from 371,500 it is a Walk job), and
`mining.submit` with the job id and the nonce as JSON integers. A share is a nonce whose Walk hash,
read as a 32-byte big-endian number, is below the share target of the job.

## Testing

- [`testing/fake_pool.py`](testing/fake_pool.py): local pool with real mainnet Walk jobs that
  re-checks every share with the official Midstate Walk code; used for benchmarks
  ([BENCHMARK.md](BENCHMARK.md)).
- `tests/fake_pool.py`: robustness test pool (malformed lines, a 3 MiB nested template, 4- and
  5-parameter jobs, a line over 4 MiB): the miner must survive and reconnect.
- Test-only options, accepted only with `--test-network` (so they cannot change mining on the real
  network): `--fee-cycle-seconds N`, `--fee-seconds M`, `--fee-pool HOST:PORT`, `--status-seconds S`
  (the plain log only: the dashboard refreshes once a second), `--log-shares` (compare with
  `tests/compare_hashes.py`).

## Layout

| Path | Content |
|---|---|
| `src/` | the miner (Walk core, assembly kernel, stratum, topology, large pages, fee, dashboard) |
| `third_party/blake3/` | official BLAKE3 C library |
| `tests/` | known-answer and cross-check tests, benchmark and profiling harnesses |
| `tools/` | Windows build script, assembly kernel generator |
| `spec/` | Walk definition, golden vectors, pool protocol |
| `testing/` | fake pool and official-code verifier for benchmarks |
| `docs/optimizations/` | performance notes: measurements, shipped changes, dead ends, next steps |
| `benchmarks/` | benchmark results and the list of CPUs to measure |

## License

Proprietary: see [LICENSE](LICENSE). Third-party components: [THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md).
