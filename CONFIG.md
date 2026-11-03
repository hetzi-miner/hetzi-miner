# Configuration

hetzi-miner is set up with one config file per coin in the `configs` folder
(`configs/etc.toml`, `configs/octa.toml`, `configs/ethw.toml`), or with command-line flags,
or both: flags override the file.

```
hetzi-miner configs/etc.toml            # mine with a config file
hetzi-miner configs/etc.toml -d 0,1     # same file, only GPUs 0 and 1
hetzi-miner -c etc -u 0xWALLET.rig1     # no file: built-in pools of the coin
hetzi-miner check configs/etc.toml      # validate the file and the pool, then exit
hetzi-miner check configs/etc.toml --connect   # also log in to every pool address
```

`start-<coin>.bat` / `start-<coin>.sh` run the matching config and restart the miner when the
watchdog asks for it. Without any argument the miner uses `miner.toml` next to it if there is
one, otherwise it asks which file from `configs` to use. On the first start it asks for the
wallet if the file still has `0xYOUR_WALLET`.

The `pools` and `logs` folders are shared and live next to the program (one level above
`configs`).

## Main settings

```toml
coin = "ETC"                 # ETC, OCTA or ETHW
wallet = "0xYOUR_WALLET"
worker = "rig1"
pools = [                    # tried top to bottom; later the miner returns to the first one
  "etc.2miners.com:1010",                 # host:port = plain TCP
  "stratum+ssl://etc.2miners.com:11010",  # TLS
  "2miners",                              # a name = every address in pools/2miners.toml
]
devices = []                 # CUDA indexes, empty = all GPUs
web_dashboard = true         # http://127.0.0.1:4077/ ; false = off
log_level = "normal"         # off | normal | verbose | trace
max_temp = 85                # pause a GPU above this temperature, °C
resume_temp = 75             # ...and resume at or below this one (default: 5 below max_temp)
dag_boost = true             # read part of the DAG from a copy in RAM for extra bandwidth
```

`log_level`: `off` (default) prints to the console only; `normal` adds a detailed log file in
`logs`; `verbose` makes the console detailed too (use it for a bug report); `trace` puts
everything into the log file, including every pool message (passwords masked).

A solo node is a pool address of the form `http://host:port`.

## Overclock

Needs administrator (Windows) or root (Linux); without that the miner mines at the driver's
settings. Everything is restored when the miner exits; `hetzi-miner --oc-reset` clears it by hand.

```toml
core_offset = -200    # core clock offset, MHz
mem_offset = 1000     # memory clock offset, MHz
core_lock = 1500      # lock the core clock, MHz (lower = undervolt, less power)
mem_lock = 8001       # lock the memory clock, MHz
power_limit = 80      # watts
fan = 70              # fixed fan speed, %
fan_target = 65       # or: hold this GPU temperature with the fans, °C
dag_reset_mem = true  # memory offset back to 0 while the DAG is built
```

Per-GPU values go in `[[gpu]]` sections; any overclock key, `max_temp` and `resume_temp` work there:

```toml
[[gpu]]
index = 0
mem_offset = 800
```

## Optional sections (shown with their defaults)

```toml
[watchdog]                 # restarts a failed or hung GPU
enabled = true
timeout_secs = 60          # no finished work for this long = hung
restarts = 3               # GPU restarts before restarting the whole miner (exit code 42)
reboot = false             # reboot the computer instead of restarting the miner

[failover]
retries = 3
retry_delay_secs = 5
return_to_primary_secs = 600
job_timeout_secs = 180

[log]
dir = "logs"
rotation = "never"         # never | daily | hourly
max_files = 30

[stats]
interval_secs = 10
dashboard = true
api = "127.0.0.1:4077"     # web dashboard, JSON at /stats; 0.0.0.0 = every interface
api_control = true         # allow POST /oc (live overclock) on a local address
api_key = "change-me"      # POST /oc then needs header X-Api-Key

[autotune]                 # launch settings tuned at every start (~10 s), kept in memory
enabled = true
```

## Pool files

`pools/<name>.toml` lists the addresses of one pool; use the name in `pools = [...]` or `-p`:

```toml
coin = "etc"
[[endpoint]]
url = "stratum+ssl://etc.2miners.com:11010"
protocol = "auto"          # auto | ethproxy | ethstratum
```

## Command-line flags

Per-GPU values are comma-separated in `-d` order, `*` skips a GPU: `--moff 1000,*,800`.

| Flag | Meaning |
|---|---|
| `-p`, `-o`, `--url POOL` | pool address, pool name or file; repeat for failover |
| `-u`, `--wallet WALLET[.WORKER]` | wallet, optionally with the worker name |
| `-w`, `--worker NAME` | worker (rig) name |
| `--pass PASSWORD` | pool password |
| `-c`, `--coin COIN` | etc (default), octa, ethw |
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

`hetzi-miner --help` lists everything.
