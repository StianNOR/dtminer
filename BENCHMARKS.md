# DTminer benchmarks

Measured results for our optimized Walk engine and the built-in XDS engine.
Walk is reported in attempts/s, XDS in H/s (one H = one complete XDS candidate:
ML-DSA-65 signature plus yespower-discrete). The two algorithms are not
directly comparable.

## Walk performance on a full CPU

| CPU | OS | Threads | Measured Walk rate | Date |
|---|---|---:|---:|---|
| AMD Threadripper PRO 5995WX, 64 cores / 128 threads | Windows 11 | 128 | **37,103–39,898 attempts/s** | 6 November 2026 |

Nine recorded runs of the optimized Walk engine used in DTminer 1.5.0, with
2 MiB large pages and the assembly step kernel. Other programs used about
8–33% of the CPU during that session.

## DTminer 1.5.0 measurements

Measured on **10 November 2026** on the same Threadripper PRO 5995WX
workstation. **The workstation was fully loaded by other work during all of
these runs (CPU at 100%)**, so these figures are lower than on an idle CPU.

| Algorithm | Build | Threads | Duration | Measured rate |
|---|---|---:|---:|---:|
| Walk | Windows x64 | 1 | 15 s | **330.7 attempts/s** |
| XDS | Windows x64 | 1 | 15–20 s | **84–86 H/s** (signature 1.2 ms + yespower-discrete 10.5–10.7 ms per candidate) |
| XDS, live on the DreamTeam XDS pool | Windows x64 | 1 | 2 min | **82 H/s** average, 43 shares accepted, 0 invalid |
| XDS | Linux x86-64, in a virtual machine | 1 | 20 s | **68.8 H/s** |
| Walk | Linux x86-64, in a virtual machine | 1 | 20 s | **263.9 attempts/s** |

The Linux figures come from an 8-CPU virtual machine running on that loaded
workstation; they show that the Linux build works, not what Linux achieves on
real hardware. No power figures were measured.

## Try your own CPU

Windows, from the extracted folder:

```bat
dtminer.exe --algo walk --benchmark 20 --threads 1
dtminer.exe --algo xds --benchmark 20 --threads 1
```

On Linux replace `dtminer.exe` with `./dtminer`. Increase the thread count for
your rig. XDS needs about 16 MiB of memory per thread and is limited by memory
bandwidth on large CPUs, so the best thread count is often below the number of
logical CPUs: measure a few values. Record the CPU, OS, thread count, duration
and background load when sharing a result.
