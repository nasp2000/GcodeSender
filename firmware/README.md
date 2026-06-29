# Firmware binaries

Version follows Node32-HUB (current: v1.07).

## Build

Compile inside [Node32-HUB](https://github.com/nasp2000/Node32-HUB) with `p4_cnc` pack:

```bash
cd chatgpt
pio run -e esp32p4
```

Post-build output (created by `rename_firmware.py`):

```
build/esp32p4/GcodeSender/v1.07/<YYYYMMDD_HHMM>/
  esp32p4_v1.07_GcodeSender_<YYYYMMDD_HHMM>.bin
  bootloader.bin
  partitions.bin
  flash_command.txt
```

## Release

1. Zip the 4 files above → `gcodesender_v1.07.zip`
2. Place the `.zip` here in `firmware/`
3. Commit and push — I will publish the GitHub release automatically

## Flash

- **First-time**: use [webflasher_Node32-HUB](https://github.com/nasp2000/webflasher_Node32-HUB) (select `gcodesender_v1.07.zip`)
- **Updates**: OTA at `http://<esp32-ip>/ota` (upload only the `.bin` file)
