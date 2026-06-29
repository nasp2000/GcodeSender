# GcodeSender-HUB

**Industrial-grade G-code sender firmware for ESP32-P4** — part of the [Node32-HUB](https://github.com/nasp2000/Node32-HUB-S3-N16R8) project.

Streams G-code to GRBL-based CNC controllers via **USB Host** (native OTG) or **UART**, with a rich web UI, PSRAM-first streaming, and multi-client session management.

---

## Features

### Transport
- **USB Host CDC-ACM** — native ESP32-P4 USB OTG (HS UTMI) → CH334F hub → Type-A ports. Auto-baud detection (cycles 115200–9600).
- **UART** — fallback HardwareSerial transport for S3-based boards.
- Unified `grblTransportWrite/Read` abstraction — same code, two backends.

### Streaming Engine
- **PSRAM-first** — entire G-code file pre-loaded into PSRAM (up to 12 MB), preprocessed in-place (comment stripping reduces size 30–50%).
- **Dedicated feeder task** — high-priority FreeRTOS task on Core 0 burst-feeds lines with zero safety margins. Never blocks on I/O.
- **Hybrid SD+PSRAM** — files larger than PSRAM are incrementally refilled from SD; PSRAM acts as a smart cache.
- **GRBL character-counting flow control** — tracks inflight bytes per line, respects the 127-byte RX buffer, serializes critical commands (`G10`, `G28`, `$X`, etc.).

### Web UI (`/grbl-controller`)
- **DRO** — Machine/Work position, max travel, axis count
- **Real-time controls** — Feed Hold, Cycle Start, Reset, Unlock, Home, Pause/Resume/Cancel
- **File browser** — SD card G-code directory with upload, delete, stream
- **PSRAM upload** — upload via HTTP, stream, view content (no SD required)
- **Local queue** — textarea quick-run with configurable error handling
- **Console** — real-time scrollable with deduped status frames
- **Production events** — alarm, error, hold, resume, done event log
- **Session control** — token-based multi-client write arbitration (operator vs observer)
- **USB diagnostics** — connected devices, line coding, auto-baud
- **64 KB debug ring buffer** — accessible via API

### Industrial Reliability
- **6 watchdogs**: USB disconnect, RX stall (30 s), ACK stall (5 s), SD read stall (5 s), inflight stall, cancel timeout
- **Error recovery**: automatic retry (configurable), out-of-order risk detection, alarm auto-clear (`$X`), partial TX corruption detection
- **G-code preprocessing** — in-PSRAM compaction before streaming starts
- **Job trace logging** — per-job CSV on SD with every TX/ACK/error annotated
- **Adaptive status polling** — interval adjusts automatically based on command throughput (30–2000 ms)

### File Transfer
| Method | Storage | Max Size | Persistence |
|--------|---------|----------|-------------|
| SD card streaming | SD → PSRAM cache | 50 MB | Permanent |
| Web upload to PSRAM | PSRAM only | 12 MB | Volatile (lost on reboot) |
| Web upload to SD | SD card | 50 MB | Permanent |
| Local queue (textarea) | Internal RAM | 8 KB | Volatile |
| Manual command | — | Line-by-line | — |

---

## Hardware

| Board | Transport | PSRAM | SD |
|-------|-----------|-------|----|
| **ESP32-P4** (recommended) | USB Host OTG (CH334F hub → Type-A) | 16 MB (12 MB usable) | SDMMC 4-bit |
| ESP32-S3 (legacy) | UART (Serial2 GPIO16/17) | 8 MB (4 MB usable) | Optional SPI |

> **Note**: Currently only ESP32-P4 is actively maintained. S3 support is available but not the primary target.

---

## Build

```bash
git clone https://github.com/nasp2000/Node32-HUB-S3-N16R8
cd chatgpt
pio run -e esp32p4 -t upload
```

Select the `GcodeSender-P4` pack in `user_config.h`:

```c
#define SELECTED_PACK PACK_P4_CNC
```

---

## Architecture

```
                    ┌─────────────────────┐
                    │   Web Browser UI    │
                    │  (PROGMEM, i18n)    │
                    └─────────┬───────────┘
                              │ HTTP / SSE
                    ┌─────────▼───────────┐
                    │   Unified Handlers  │  27 API endpoints
                    │  (handlers_unified) │
                    └─────────┬───────────┘
                              │
              ┌───────────────┼───────────────┐
              │               │               │
     ┌────────▼───────┐ ┌────▼────┐ ┌────────▼───────┐
     │  grbl_web_api  │ │ File I/O│ │ grbl_sender_*  │
     │ (token auth,   │ │ (SD,    │ │ (state machine, │
     │  SSE events)   │ │ PSRAM) │ │  streaming, RX) │
     └────────────────┘ └─────────┘ └────────┬────────┘
                                             │
                                 ┌───────────▼───────────┐
                                 │  grblTransportWrite()  │
                                 │  (USB Host or UART)    │
                                 └───────────┬───────────┘
                                             │
                                    ┌────────▼────────┐
                                    │  GRBL Controller │
                                    │  (CNC firmware)  │
                                    └─────────────────┘
```

---

## License

Same as Node32-HUB — see the [main repository](https://github.com/nasp2000/Node32-HUB-S3-N16R8).
