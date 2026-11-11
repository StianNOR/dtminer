# DTminer

**DTminer v1.5.1 — Walk + XDS. One DreamTeam CPU miner.**

DTminer brings **our optimized Walk engine** and **Discrete (XDS) mining**
together in one program for Windows and Linux. Follow your hashrate, accepted
shares and connection status in the live terminal dashboard.

[**Download DTminer v1.5.1**](https://github.com/StianNOR/dtminer/releases/tag/v1.5.1)
 · [Benchmark results](BENCHMARKS.md)

## Built for CPU mining

- **Our optimized Walk engine:** hand-tuned assembly and automatic selection of
  the available CPU acceleration for Midstate (MDS) and Midwimble (MW).
- **Built-in XDS engine:** Discrete's identity-bound proof of work — ML-DSA-65
  signing and yespower-discrete — runs inside DTminer. One executable, no helper
  program.
- **Secure pool connection for XDS:** HTTPS with certificate and host-name
  verification, always on.
- **Windows and Linux:** complete archives with launch examples and license notices.
- **A clear view of your miner:** hashrate, accepted shares and pool status.

## Performance snapshot

The following measurements were recorded with the v1.5.0 hashing engines, which
remain unchanged in v1.5.1. These are historical benchmark results, **not new
v1.5.1 measurements**.

AMD Threadripper PRO 5995WX, Windows 11:

| Algorithm | Threads | Measured rate |
|---|---:|---:|
| Walk, optimized engine¹ | 128 | **37.1–39.9k attempts/s** |
| Walk, same engine (v1.5.0 measurement) | 1 | **331 attempts/s**² |
| XDS (v1.5.0 measurement) | 1 | **84–86 H/s**² |
| XDS, live pool (v1.5.0 measurement) | 1 | **82 H/s**² |

¹ Measured on 6 November 2026 with the same optimized Walk engine.
² Measured on 10 November 2026 on a workstation that was fully loaded by other
work at the time; an idle CPU is faster. [Full results](BENCHMARKS.md).

## Easy install

Open the setup page for your pool, enter your public payout address or addresses
and thread count, then copy the generated one-line command to install and run dtminer.

| Pool | Setup | Dashboard |
| --- | --- | --- |
| Midstate + Midwimble (Walk) | [Install for MDS + MW](https://dreamteamcrypto.xyz/pool-mds-mw-connect) | [Pool stats](https://dreamteamcrypto.xyz/pool-mds-mw) |
| Discrete (XDS) | [Install for Discrete](https://dreamteamcrypto.xyz/pool-discrete-connect) | [Pool stats](https://dreamteamcrypto.xyz/pool-discrete) |

## Get started with v1.5.1

1. Download the archive for your operating system and extract the **whole folder**.
2. Add your **public payout address** to the matching launch example.
3. Start Walk or XDS. Use Ctrl+C to stop the miner.

| Algorithm | Windows | Linux | What to edit |
|---|---|---|---|
| Walk (MDS / MW) | `START-WALK.bat` | `./start-walk.sh` | MDS and MW public payout addresses |
| XDS | `START-XDS.bat` | `./start-xds.sh` | Your public XDS address in `xds-address.txt` |

The launch examples start with **one CPU thread**. Change the thread count to
suit your rig; `--threads auto` selects it automatically. Each XDS thread uses
about 17 MiB of memory.

### Command-line examples

```text
dtminer --algo walk --pool mds-mid.dreamteamcrypto.xyz:3336 --mds YOUR_MDS_ADDRESS --mw YOUR_MW_ADDRESS --worker rig1 --threads auto
dtminer --algo xds --pool https://xds.dreamteamcrypto.xyz:9340 --address-file xds-address.txt --worker rig1 --threads auto
```

Use `dtminer.exe` in Windows Command Prompt or `./dtminer` on Linux.
`--no-ui` selects plain logs. `--list-algos` lists the algorithms;
`--algo walk --help` and `--algo xds --help` show their options.

| Pool | Mining endpoint | Dashboard |
|---|---|---|
| Midstate + Midwimble | `mds-mid.dreamteamcrypto.xyz:3336` | [MDS / MW](https://dreamteamcrypto.xyz/pool-mds-mw) |
| Discrete / XDS | `https://xds.dreamteamcrypto.xyz:9340` | [XDS](https://dreamteamcrypto.xyz/pool-discrete) |

Walk work starts at MDS height **371500** and MW height **37300**. DTminer waits
when the pool supplies BLAKE3-only jobs. The XDS mining endpoint must include
**HTTPS and port 9340**.

## Requirements and fees

- **CPU:** x86-64 with SSE4.2 (x86-64-v2). AVX2 is **not** required. Walk uses
  AES-NI and BMI2 when present and falls back to portable code otherwise.
- **Windows x64:** tested on Windows 11. No extra runtime to install.
- **Linux x86-64:** the verified Ubuntu 22.04 build requires at most glibc **2.34** (tested on Ubuntu 22.04).
  HiveOS has not yet been tested.
- **Miner developer fee:** Walk **2%**; XDS **0%**. Pool fees are separate.

The miner needs public payout addresses only. Never enter a wallet seed or
private key. For XDS, the pool assigns a mining key to each rig; DTminer creates
a private rig identity file automatically in your user profile, outside the
download folder (Windows: `%LOCALAPPDATA%\DTminer\xds-native\`, Linux:
`~/.local/state/dtminer/xds-native/`). Keep that file private; `--secret-file`
chooses another location.

Run an offline check at any time:

```text
dtminer --algo walk --self-test
dtminer --algo xds --self-test
```

The XDS self-test checks the built-in engine against published known answers
and real Discrete mainnet blocks. DTminer also runs it before it starts mining.

## Binary distribution and license

DTminer is **proprietary, closed-source software**. This public repository provides
release binaries and user documentation, **not the implementation source code**.
Downloading a binary does not grant permission to publish the proprietary source,
modify the proprietary executable, or distribute modified versions. The binary
distribution terms permit use for mining and redistribution of complete, unmodified
official packages with their notices and checksums. Third-party components retain
their own licenses and rights. See the binary terms and component notices in the
downloaded release archive.

Always download from the [official releases](https://github.com/StianNOR/dtminer/releases)
and verify the published SHA-256 checksums. A GitHub fork of this public download
repository does **not** grant rights to the proprietary miner implementation.
