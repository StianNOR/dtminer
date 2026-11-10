# DTminer

**Your CPU. Walk + XDS. One DreamTeam miner.**

DTminer brings **our optimized Walk engine** and **Discrete (XDS) mining**
together in a complete package for Windows and Linux. Follow your hashrate,
accepted shares and connection status in the live terminal dashboard.

[**Download DTminer 1.5.0**](https://github.com/StianNOR/dtminer/releases)
 · [Benchmark results](BENCHMARKS.md)

## Built for CPU mining

- **Our optimized Walk engine:** hand-tuned assembly and automatic selection of
  the available CPU acceleration for Midstate (MDS) and Midwimble (MW).
- **XDS support:** mine Discrete from the same command line and dashboard.
- **Windows and Linux:** complete archives with launch examples and everything
  needed for the supported mining profiles.
- **A clear view of your miner:** hashrate, accepted shares and pool status.

## Performance snapshot

AMD Threadripper PRO 5995WX, Windows 11:

| Algorithm | Threads | Measured rate |
|---|---:|---:|
| Walk, optimized engine¹ | 128 | **37.1–39.9k attempts/s** |
| Walk, DTminer 1.5.0 | 1 | **464.2 attempts/s** |
| XDS, DTminer 1.5.0 | 1 | **85.4 H/s** |

¹ The 128-thread Walk results were measured on 6 November 2026 with the same
optimized hashing engine. The current 1.5.0 single-thread samples were measured
on 10 November, for 20 seconds each on a shared workstation. Performance depends
on hardware, settings and CPU load. [Full results and Linux measurements](BENCHMARKS.md).

## Get started

1. Download the archive for your operating system and extract the **whole folder**.
2. Add your **public payout address** to the matching launch example.
3. Start Walk or XDS. Use Ctrl+C to stop the miner.

| Algorithm | Windows | Linux | What to edit |
|---|---|---|---|
| Walk (MDS / MW) | `START-WALK.bat` | `./start-walk.sh` | MDS and MW public payout addresses |
| XDS | `START-XDS.bat` | `./start-xds.sh` | Your public XDS address in `xds-address.txt` |

The launch examples start with **one CPU thread**. Change the thread count to
suit your rig; `--threads auto` enables automatic selection. Keep the bundled
`engines/` folder with the main executable.

### Command-line examples

```text
dtminer --algo walk --pool mds-mid.dreamteamcrypto.xyz:3336 --mds YOUR_MDS_ADDRESS --mw YOUR_MW_ADDRESS --worker rig1 --threads auto
dtminer --algo xds --pool https://xds.dreamteamcrypto.xyz:9340 --address-file xds-address.txt --worker rig1 --threads auto
```

Use `dtminer.exe` in Windows Command Prompt or `./dtminer` on Linux.
`--no-ui` selects plain logs. `--list-algos` lists the supported profiles;
`--algo walk --help` and `--algo xds --help` show their options.

| Pool | Mining endpoint | Dashboard |
|---|---|---|
| Midstate + Midwimble | `mds-mid.dreamteamcrypto.xyz:3336` | [MDS / MW](https://mds-mid.dreamteamcrypto.xyz/pool/midwimble/) |
| Discrete / XDS | `https://xds.dreamteamcrypto.xyz:9340` | [XDS](https://xds.dreamteamcrypto.xyz/) |

Walk work starts at MDS height **371500** and MW height **37300**. DTminer waits
when the pool supplies BLAKE3-only jobs. The XDS mining endpoint must include
**HTTPS and port 9340**, even though its browser dashboard uses the normal web port.

## Requirements and fees

- **Windows x64:** tested on Windows 11. XDS requires an AVX2-capable CPU.
- **Linux x86-64:** glibc **2.35 or newer** for XDS; tested on Ubuntu 22.04/WSL2.
  HiveOS has not yet been tested.
- **Miner developer fee:** Walk **2%**; XDS **0%**.
  Pool fees are separate.

The miner needs public payout addresses. Never enter a wallet seed or private key.
XDS manages its rig identity file automatically in the user profile, outside the
downloaded folder. Keep that file private. For Windows profiles containing
non-ASCII characters, use `--secret-file` with an ASCII-only path outside the
downloaded folder. Non-ASCII Windows paths remain untested.

Run an offline check at any time:

```text
dtminer --algo walk --self-test --threads 1
dtminer --algo xds --self-test --threads 1
```

DTminer is distributed as ready-to-run binaries. Binary terms and third-party
component notices/license texts are included in each download.
