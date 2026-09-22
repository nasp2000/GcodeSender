# Firmware binaries

Version follows Node32-HUB.

## Current release

- **v1.114**: `esp32p4_v1.114_P4_CNC_20260921_0433.zip`

## Build

Compile inside [Node32-HUB](https://github.com/nasp2000/Node32-HUB) with `p4_cnc` pack:

```bash
cd chatgpt
pio run -e esp32p4
```

Post-build output (created by `rename_firmware.py`):

```
build/esp32p4/P4_CNC/v1.114/<YYYYMMDD_HHMM>/
  esp32p4_v1.114_P4_CNC_<YYYYMMDD_HHMM>.bin   # + bootloader.bin, partitions.bin, flash_command.txt
  LICENSE, SBOM.md, THIRD_PARTY_NOTICES, README_DISTRIBUTION.txt, third-party.zip
```

## Release

1. Zip `bootloader.bin`, `partitions.bin`, `.bin` and `flash_command.txt` (from the build folder above) → `esp32p4_v1.<ver>_P4_CNC_<YYYYMMDD_HHMM>.zip`
2. Include the compliance set: `LICENSE`, `SBOM.md`, `THIRD_PARTY_NOTICES`, `README_DISTRIBUTION.txt`, `third-party.zip`
3. Place the `.zip` here in `firmware/`
4. Commit and push — the GitHub release is published automatically

## Flash

- **First-time**: use [webflasher_Node32-HUB](https://github.com/nasp2000/webflasher_Node32-HUB) (select `esp32p4_v1.114_P4_CNC_20260921_0433.zip`)
- **Updates**: OTA at `http://<esp32-ip>/ota` (upload only the `.bin` file)