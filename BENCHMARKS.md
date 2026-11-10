# DTminer benchmarks

Measured results for our optimized Walk engine and XDS. Walk is reported in
attempts/s; XDS in H/s. The rates belong to different algorithms and are not
directly comparable with each other.

## Walk performance on a full CPU

| CPU | OS | Threads | Measured Walk rate | Date |
|---|---|---:|---:|---|
| AMD Threadripper PRO 5995WX, 64 cores / 128 threads | Windows 11 | 128 | **37,103–39,898 attempts/s** | 6 November 2026 |

These are nine recorded runs of the optimized Walk engine carried into DTminer
1.5.0. Best runs were 39,582–39,898 attempts/s. Other programs used approximately
8–33% of the CPU during that session. The configuration used 2 MiB large pages
and the assembly step kernel. This is a historical engine result; a new 128-thread
run of the 1.5.0 host was not performed.

## Fresh DTminer 1.5.0 measurements

All four samples below were measured on **10 November 2026**, on the same
Threadripper PRO 5995WX workstation, with **one CPU thread for 20 seconds per
algorithm**. The machine was running other workloads. Algorithms and operating
systems were measured one after another, offline, with no pool or GPU activity.

| Algorithm | OS | Threads | Measured rate |
|---|---|---:|---:|
| Walk | Windows 11 | 1 | **464.2 attempts/s** |
| Walk | Ubuntu 22.04 / WSL2 | 1 | **482.4 attempts/s** |
| XDS | Windows 11 | 1 | **85.4 H/s** |
| XDS | Ubuntu 22.04 / WSL2 | 1 | **86.1 H/s** |

These short samples demonstrate measured rates for the released builds.
They are not maximum full-rig rates or a controlled Windows-versus-Linux
comparison. CPU clocks and background load varied; no power figures were measured.
The XDS benchmark includes local signing and proof-of-work calculation.

## Try your own CPU

Windows, from the extracted folder:

```bat
dtminer.exe --algo walk --benchmark 20 --threads 1
dtminer.exe --algo xds --benchmark 20 --threads 1
```

On Linux replace `dtminer.exe` with `./dtminer`. Increase the thread count for
your rig, or use `--threads auto` when you want automatic CPU thread selection.
Use production parameters and record the CPU, OS, thread count, duration and
background load when sharing a result.

Builds measured (SHA-256 of the host executable):

- Windows: `6078cf8ff76e6c0653003039ee15265300b1fecfa6842ce8b2d178998c6abf0f`
- Linux: `181b7fee1832572d95ca253159728ae4c9965cdc7b5e5b8d3e4656a1c458a861`
