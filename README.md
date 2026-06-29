# GcodeSender

**G-code sender firmware for ESP32-P4** — part of the [Node32-HUB](https://github.com/nasp2000/Node32-HUB) project.

Streams G-code to GRBL-based CNC controllers via **USB Host**, with a web-based control panel, automatic error recovery, and dual storage (SD card + PSRAM).

---

## Features

### Storage
- **SD card** — primary storage. Upload files via the web UI or copy directly to the card. The sender streams directly from SD.
- **PSRAM** — fallback when no SD card is present. Upload files via the web UI to the board's built-in PSRAM. Contents are lost on reboot.
- **Hybrid mode** — when both are available, the file is loaded into PSRAM for faster streaming. If the file is too large, the remainder is fetched incrementally from SD.

### Transport
- **USB Host** — connect directly to a GRBL controller via USB (ESP32-P4 native OTG).

### Web UI (`/grbl-controller`)
- Machine position display (DRO) with real-time updates
- Start, pause, resume, cancel, feed hold, reset, unlock, home
- Browse and stream G-code files from SD card
- Upload files to SD card or to PSRAM (no SD needed)
- Text area for quick commands (local queue)
- Real-time console log with status feedback
- Event log for alarms, errors, and state changes
- Multi-session support (one operator, multiple observers)
- USB diagnostics and auto-baud detection

### Reliability
- Automatic retry on errors, alarm auto-clear (`$X`), and stall detection
- G-code preprocessing — removes comments and blank lines before sending
- Job logging to SD card for debugging
- Adaptive polling — adjusts to command throughput automatically

---

## Build

```bash
git clone https://github.com/nasp2000/Node32-HUB
cd chatgpt
pio run -e esp32p4 -t upload
```

Select the `GcodeSender-P4` pack in `user_config.h`:

```c
#define SELECTED_PACK PACK_P4_CNC
```

---

## License

Same as Node32-HUB — see the [main repository](https://github.com/nasp2000/Node32-HUB).
