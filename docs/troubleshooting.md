# Troubleshooting

Common issues and how to fix them. If your problem isn't listed, open an issue with your hardware setup, Bruce firmware version, and what's not working.

---

## Module Not Detected

**Most common cause:** `brucePins.conf` wasn't applied properly. Check:

1. Did you save the config to the **SD card**?
2. Did you also save it to **LittleFS**? (Both are needed — Bruce reads from one or the other depending on version.)
3. Did you **restart** the Cardputer ADV after saving?
4. Are the values exactly: `cc1101 cs=13 io0=5` and `nrf24 cs=6 io0=4`?

**Second most common cause:** wiring error. With a multimeter in continuity mode:

- Cardputer EXT 7 ↔ CC1101 SCK ↔ NRF24 SCK → **should** beep (shared bus)
- Cardputer EXT 9 ↔ CC1101 MOSI ↔ NRF24 MOSI → **should** beep
- Cardputer EXT 11 ↔ CC1101 MISO ↔ NRF24 MISO → **should** beep
- CC1101 CSN ↔ NRF24 CSN → **should NOT** beep (independent CS lines)

If the last check beeps, you've shorted the chip selects — neither radio will work. Rewire and retest.

**Still not working?** Try erasing the device, reflashing Bruce, reformatting the SD card, and re-applying the configuration.

---

## Only One Radio Works

If CC1101 works but NRF24 doesn't (or vice versa), the problem is specific to the failing radio's wiring or its CSN line.

Check the failing radio's:
- **CSN line**: matches the value in `brucePins.conf` (mixing up which CSN goes to which chip is a common error)
- **Power**: measure 3.3V at the chip's actual VCC pin, not just at the regulator
- **Module health**: try a replacement if you have one — ESD damage is rare but possible

---

## CC1101 Only Works After Opening NRF24

Known Bruce firmware behavior — not a wiring issue. See [Bruce issue #1909](https://github.com/pr3y/Bruce/issues/1909).

**Workaround:** on each fresh boot, open any NRF24 tool first (e.g. NRF Spectrum). Then switch to CC1101 — it'll work normally.

---

## Cardputer Reboots During Record Raw

Known limitation of the Cardputer ADV's shared SPI bus design. Not specific to this module — it affects any RF expansion.

**Workaround:** remove the SD card before using Record Raw. The capture buffers to LittleFS instead, which doesn't compete with the expansion bus.

---

## Reboots During Long Transmission

If the device reboots during TX (especially with high-power modules like AS01-ML01DP5):

**Cause:** the LDO can't supply enough current during TX bursts. The 3.3V rail sags and the ESP32 brown-outs.

**Fixes (try in order):**
1. Add more bulk capacitance on the 3.3V rail (470 µF electrolytic + 10 µF tantalum near each module)
2. Use a higher-current LDO (≥1 A capacity)
3. Switch to a DCDC buck converter

If TX stability matters to you and DIY adjustments aren't enough, the production [Hydra RF series](../README.md#also-available) uses DCDC for this exact reason.

---

## Weak or No RF Range

Most common reasons, in order:

1. **Wrong antenna for the frequency.** Match the antenna band to your operating frequency (433 / 868 / 915 MHz, or 2.4 GHz).
2. **Long jumper wires.** Wires longer than ~5 cm act as parasitic antennas at RF frequencies. Keep RF connections short — for serious range testing, build on a PCB rather than breadboard.
3. **Loose SMA connection.** Finger-tighten only (no tools — they damage the threads).
4. **LDO sagging under TX.** See the previous section.
5. **Indoor environment.** Concrete and metal attenuate RF significantly — test outdoors first.

---

## WebUI Won't Load

- Make sure the Cardputer is connected to the same Wi-Fi network as the device you're browsing from
- Re-read the IP from the Cardputer screen (it can change between sessions)
- Try a different browser or incognito mode
- Test from a phone on the same network to rule out PC firewall issues

---

## Need More Help?

Open a [GitHub issue](../../issues) with:

1. Hardware (which modules, which regulator)
2. Wiring (photos help)
3. Bruce firmware version
4. What you've already tried
