<p align="center"><img src="logo.png" width="96" alt="hetzi-miner"></p>

<h1 align="center">hetzi-miner</h1>

<p align="center">
Multi-coin GPU miner for NVIDIA cards<br>
on Windows, Linux and HiveOS.
</p>

<p align="center">
<b><a href="https://github.com/hetzi-miner/hetzi-miner/releases/tag/v0.2.0">Latest: v0.2.0</a></b> (2026-11-14) ·
<a href="https://www.hetzi-miner.com">Website</a> ·
<a href="https://github.com/hetzi-miner/hetzi-miner/releases">All releases</a> ·
<a href="CHANGELOG.md">Changelog</a>
</p>

## Download

| Platform | File | SHA256 |
|---|---|---|
| Windows | [hetzi-miner-0.2.0-win64.zip](https://github.com/hetzi-miner/hetzi-miner/releases/download/v0.2.0/hetzi-miner-0.2.0-win64.zip) ([mirror](https://www.hetzi-miner.com/download/v0.2.0/hetzi-miner-0.2.0-win64.zip)) | `2ad4e768fa15a860c851b441149fce0823ad1308f2caf86be7b5f11ce3079a0e` |
| Linux | [hetzi-miner-0.2.0-linux.tar.gz](https://github.com/hetzi-miner/hetzi-miner/releases/download/v0.2.0/hetzi-miner-0.2.0-linux.tar.gz) ([mirror](https://www.hetzi-miner.com/download/v0.2.0/hetzi-miner-0.2.0-linux.tar.gz)) | `89ff0164b686fae4a518c568386525561ef84747bd1a2f0849779b07d1b2621e` |
| HiveOS | [hetzi-miner-0.2.0.tar.gz](https://github.com/hetzi-miner/hetzi-miner/releases/download/v0.2.0/hetzi-miner-0.2.0.tar.gz) ([mirror](https://www.hetzi-miner.com/download/v0.2.0/hetzi-miner-0.2.0.tar.gz)) | `fd07173974863bfaee434ff64a52c8211e238b95d3a399aea4ad21bb1bac63a1` |

The same files are on the mirror (links above) and on [the website](https://www.hetzi-miner.com/releases/).
Check a download against `SHA256SUMS`:

- Windows: `certutil -hashfile hetzi-miner-0.2.0-win64.zip SHA256`
- Linux: `sha256sum -c SHA256SUMS --ignore-missing`

## Quick start

**Windows.** Unpack the zip and run `start-etc.bat` (or `start-octa.bat`, `start-ethw.bat`, `start-qtc.bat`).
On the first start the miner asks for your wallet address and saves it in `configs\etc.toml`.
Or from the command line:

```
hetzi-miner.exe -p etc.2miners.com:11010 --tls -u 0xYOUR_WALLET.rig1
```

**Linux.**

```
tar xzf hetzi-miner-0.2.0-linux.tar.gz && cd hetzi-miner-0.2.0-linux && ./start-etc.sh
```

**HiveOS.** Flight sheet → Miner: Custom.

| Field | Value |
|---|---|
| Installation URL | `https://www.hetzi-miner.com/download/v0.2.0/hetzi-miner-0.2.0.tar.gz` |
| Hash algorithm | `etchash` |
| Wallet and worker template | `%WAL%.%WORKER_NAME%` |
| Pool URL | `stratum+ssl://etc.2miners.com:11010` |

The web dashboard with hashrate, temperatures and shares is at http://127.0.0.1:4077/ while the miner runs.

## Configuration

Each coin has its own file in `configs` (`etc.toml`, `octa.toml`, `ethw.toml`): wallet, worker,
pools with failover, GPUs, temperature limits, overclock, logging. Flags override the file:

```
hetzi-miner configs/etc.toml -d 0,1 --moff 1000 --pl 120
```

```toml
coin = "ETC"
wallet = "0xYOUR_WALLET"
worker = "rig1"
pools = ["stratum+ssl://etc.2miners.com:11010", "etc.2miners.com:1010"]
devices = []          # empty = all GPUs
max_temp = 85
# mem_offset = 1000   # overclock: needs administrator / root, restored on exit
```

Every key and the optional sections: **[CONFIG.md](CONFIG.md)**.

## Command-line flags

Flags work on their own or after a config file, overriding it. Per-GPU values are
comma-separated in `-d` order, `*` skips a GPU: `--moff 1000,*,800`.

| Flag | Meaning |
|---|---|
| `-p`, `-o`, `--url POOL` | pool address, pool name or file; repeat for failover |
| `-u`, `--wallet WALLET[.WORKER]` | wallet, optionally with the worker name |
| `-w`, `--worker NAME` | worker (rig) name |
| `--pass PASSWORD` | pool password |
| `-c`, `--coin COIN` | etc (default), octa, ethw, qtc |
| `--tls` | TLS for host:port addresses |
| `--protocol P` | auto, ethproxy, ethstratum, getwork |
| `-d`, `--devices 0,1` | GPUs to use |
| `--max-temp C`, `--tstart C` | pause above / resume at temperature |
| `--coff`, `--moff MHZ` | core / memory clock offset |
| `--cclk`, `--mclk MHZ` | lock core / memory clock |
| `--pl W`, `--fan %`, `--fan-target C` | power limit, fan speed, fan target temperature |
| `--dag-boost on/off` | DAG boost (share tuned automatically) |
| `--zombie auto/off` | mine when the DAG does not fit in VRAM (rest in system RAM) |
| `--no-watchdog`, `--watchdog-timeout S`, `--watchdog-reboot` | watchdog |
| `--api ADDR`, `--no-api`, `--api-key KEY` | web dashboard / stats API |
| `--no-dashboard` | plain scrolling log instead of the live GPU table |
| `--log-level L`, `-v`, `--trace`, `--debug-protocol` | logging |
| `--oc-reset` | clear all clock, power and fan settings, then exit |

`hetzi-miner --help` lists everything; `hetzi-miner check configs/etc.toml` validates a config without mining.

## Supported GPUs and coins

| Generation | Cards | Driver |
|---|---|---|
| Pascal | GTX 10xx, P104/P106, Tesla P | R525 – R580 |
| Volta (experimental) | Titan V, Tesla V100 | R525 – R580 |
| Turing | RTX 20xx, GTX 16xx | R525+ |
| Ampere | RTX 30xx, A-series | R525+ |
| Ada Lovelace | RTX 40xx | R525+ |
| Hopper | H100 | R525+ |
| Blackwell | RTX 50xx | R570+ |

NVIDIA drivers newer than R580 no longer support Pascal and Volta. Volta is not tested on real
cards yet; reports in Issues are welcome.

| Coin | Algorithm | GPU memory |
|---|---|---|
| Ethereum Classic (ETC) | Etchash | 6 GB+ |
| OctaSpace (OCTA) | Ethash | 4 GB+ |
| EthereumPoW (ETHW) | Ethash | 10 GB+ (8 GB in zombie mode) |
| Quantus (QTC) | qpow-poseidon2 | no DAG |

## Features

- **DAG boost:** +3–5% hashrate on cards where the DAG fits in VRAM.
- **Zombie mode:** keeps mining when the DAG outgrows VRAM.
- **Checked shares:** every share is verified on the CPU before sending.
- **Failover and TLS:** backup pools with automatic return to the main one.
- **Web dashboard, watchdog, overclock:** clocks are restored on exit.
- **Auto-tune:** two-stage tuning picks the best launch settings for each GPU.
- **Quantus (QTC):** hand-scheduled kernels for GTX 10xx and RTX 20/30/40/50; built-in pools Kryptex, HeroMiners, LuckyPool.
- **Robust:** restarts after a crash, skips dead GPUs, pool and extranonce failover.

## Dev fee

1% of the hashrate on every coin goes to the developer. The fee is mined continuously
(no switching pauses) through a separate pool connection. The hashrate the miner shows includes the fee; the fee hashrate is shown separately in the dashboard.

## Windows Defender

Antivirus programs often flag GPU miners, and Defender may report this one as
`Trojan:Win32/Bearfoos.A!ml`. That is a heuristic, not a known threat. Download only
from this page or https://www.hetzi-miner.com, compare the SHA256 above, then add the miner folder to the
Defender exclusions if needed.

## Support

Bugs and questions: [Issues](https://github.com/hetzi-miner/hetzi-miner/issues). Please include your GPU, driver version,
OS and the log from the `logs` folder. Security problems: report them privately via
[Security → Report a vulnerability](https://github.com/hetzi-miner/hetzi-miner/security/advisories/new).

The source code is not published; this repository holds release notes and binaries.
