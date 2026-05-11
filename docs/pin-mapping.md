# Pin Mapping Reference

Full GPIO assignments for the CC1101 + NRF24 dual-radio setup on the M5Stack Cardputer ADV.

---

## Quick Reference

| Function | GPIO | Direction | Header Pin |
|---|---|---|---|
| SCK | GPIO 40 | OUT | EXT 7 |
| MOSI | GPIO 14 | OUT | EXT 9 |
| MISO | GPIO 39 | IN | EXT 11 |
| CC1101 CSN | GPIO 13 | OUT | EXT 13 |
| CC1101 GDO0 | GPIO 5 | IN | (extension) |
| NRF24 CSN | GPIO 6 | OUT | EXT 5 |
| NRF24 CE | GPIO 4 | OUT | EXT 3 |
| 5V in | — | — | EXT 6 (5VOUT) |
| GND | — | — | EXT 4 |

Not connected: CC1101 GDO2, CC1101 RST, NRF24 IRQ, NRF24 RST.

---

## Cardputer ADV EXT Header Map

The 14-pin extension header on Cardputer ADV. Pins marked ✅ are used by this design.

| Pin | Default Label | Used? | Purpose |
|---|---|---|---|
| 1 | RESET (G3) | — | free |
| 2 | 5VIN | — | free |
| 3 | INT (G4) | ✅ | NRF24 CE |
| 4 | GND | ✅ | ground |
| 5 | BUSY (G6) | ✅ | NRF24 CSN |
| 6 | 5VOUT | ✅ | 5V to regulator |
| 7 | SCK (G40) | ✅ | shared SPI clock |
| 8 | I2C_SDA (G8) | — | free |
| 9 | MOSI (G14) | ✅ | shared SPI MOSI |
| 10 | I2C_SCL (G9) | — | free |
| 11 | MISO (G39) | ✅ | shared SPI MISO |
| 12 | UART_RX (G13) | ✅ | CC1101 CSN |
| 13 | CS (G5) | ✅ | CC1101 GDO0 |
| 14 | UART_TX (G15) | — | free |

The "Default Label" reflects M5Stack's documentation. Several pins are repurposed in this design — that's normal on ESP32-S3, which allows flexible GPIO assignment.

---

## Pre-Power-On Multimeter Check

A 2-minute check that catches most wiring mistakes:

**Continuity (should beep):**
- EXT 7 ↔ CC1101 SCK ↔ NRF24 SCK
- EXT 9 ↔ CC1101 MOSI ↔ NRF24 MOSI
- EXT 11 ↔ CC1101 MISO ↔ NRF24 MISO

**Continuity (should NOT beep):**
- CC1101 CSN ↔ NRF24 CSN — these are independent and must NOT be tied together

**Voltage (with regulator only, modules disconnected):**
- LDO output reads 3.3V ±0.1V

If the CSN-to-CSN check beeps, stop and rewire — the build won't work otherwise.
