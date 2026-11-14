# Changelog

New versions go on top. The release job takes the section of its version as the
release notes (GitHub, website); the first line is the one-line summary.

## [0.2.0]

Quantus (QTC) mining.

- Quantus (QTC) on NVIDIA GTX 10xx – RTX 50 with hand-scheduled kernels per architecture
- `-a quantus` mines on the built-in QTC pools without a config file
- Pascal (GTX 10xx) supported and tested on real cards
- Auto-tune while mining, picked by useful hash rate
- Faster job switch and share submit; late pool answers no longer count as rejected
- Built-in QTC pools: Kryptex, HeroMiners, LuckyPool; `configs/qtc.toml` and `start-qtc.bat` / `start-qtc.sh`
- Two-stage auto-tune
- Robustness: restart after a crash, dead GPUs are skipped, pool and extranonce failover
- Linux and HiveOS builds (HiveOS algorithm: qpow-poseidon2)
- Dev fee: 1% on every coin (0.5% before); the hashrate shown includes the fee, the fee hashrate is shown separately

## [0.1.0]

First public release.

- ETC, OCTA and ETHW on NVIDIA GTX 10xx – RTX 50
- Volta (Titan V, V100): experimental, not tested on real cards yet
- Windows, Linux and HiveOS
- DAG boost and zombie mode
- Pool failover and TLS
- Web dashboard, watchdog, overclock
