# drew2323/aircannect fork

Fork of [m-kozlowski/aircannect](https://github.com/m-kozlowski/aircannect) with local patches
that must be carried across upstream updates.

## Local patches (branch `n16r8-sdmmc1`)

1. `src/storage_manager.cpp` – `sdmmc_prepare_pins()`: enables ESP32-S3 internal pull-ups on
   SDMMC CMD/DATx (Arduino `SD_MMC` passes slot flags = 0) and, with
   `AC_SDMMC_D3_HOLD_HIGH_GPIO`, drives card DAT3 high so a 1-bit SDMMC card stays in SD mode.
2. `platformio.ini` – env `esp32s3-n16r8-sdmmc1`: generic ESP32-S3 N16R8 + passive SPI SD
   breakout (regulator + resistors, no level shifter) rewired in software to 1-bit SDMMC,
   10 MHz.

## Wiring (same wires as the SPI variant)

| module pin | ESP32-S3 GPIO | SDMMC role |
|---|---|---|
| GND | GND | ground |
| 3V3 | 3V3 | card power (5V pin unused) |
| SCK | 12 | CLK |
| MOSI | 11 | CMD |
| MISO | 13 | DAT0 |
| CS | 10 | DAT3, held high |

After switching from an SPI build, power-cycle the board (unplug USB) – the card stays in SPI
mode until it loses power.

## Verification

32 MiB upload via `/api/storage/upload` + download readback: SHA-256 match, no remount.
SPI at 1–5 MHz on the same hardware failed with `write_failed`.

## Updating from upstream

```sh
git fetch upstream
git rebase upstream/master   # on branch n16r8-sdmmc1
pio run -e esp32s3-n16r8-sdmmc1
```
