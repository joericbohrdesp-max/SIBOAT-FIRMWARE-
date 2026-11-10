# SIBOAT firmware

This repository compiles the two SIBOAT sketches into .bin files that you flash over USB.

| Board | Sketch | Version |
| --- | --- | --- |
| Main ESP32 | SIBOAT_MAIN-ESP32_FIRMWARE_FINAL_BOSS.ino | 1.7.1-manual-first |
| ESP32-CAM | SIBOAT_ESP32-CAM_FIRMWARE_FINAL_BOSS.ino | 1.5.0-camlink-ota |

## Files

- `.github/workflows/build-firmware.yml`: the build. It only compiles.
- `SIBOAT_MAIN-ESP32_FIRMWARE_FINAL_BOSS.ino` and `siboat_main_types.h`: Main ESP32 firmware and its helper header.
- `SIBOAT_ESP32-CAM_FIRMWARE_FINAL_BOSS.ino`: ESP32-CAM firmware.
- `siboat_inferencing.zip`: the camera AI model (Edge Impulse FOMO, Arduino library).
- `siboat_secrets.example.h`: template for your Wi-Fi settings. Never type real passwords into this file.
- `SIBOAT_TRANSPORT_PREFERENCE.sql`: Supabase SQL, run once.

## Set up once

1. Create a new repository and set it to **Private**.
2. Upload every file from this folder to the top level of the repository.
3. The build file must end up at `.github/workflows/build-firmware.yml`. If your upload skipped the `.github` folder: Add file > Create new file, type that path as the file name, and paste the text of the build file.
4. Add your Wi-Fi settings as a secret: Settings > Secrets and variables > Actions > New repository secret. Name: `SIBOAT_SECRETS_H`. Value: the whole text of your `siboat_secrets.h` (start from `siboat_secrets.example.h`, then put your real Wi-Fi names and passwords in it). The build stops with a clear message if the secret is missing, if the repository is public, or if the first password is still `CHANGE_ME`, because a board built that way could not join Wi-Fi.
5. Open the Supabase SQL editor and run `SIBOAT_TRANSPORT_PREFERENCE.sql` once. It is safe to run again.

## Build

Actions tab > Build SIBOAT firmware > Run workflow. When both jobs are green, open the run and download the two files under Artifacts:

- `SIBOAT_MAIN-ESP32_FIRMWARE_FINAL_BOSS`
- `SIBOAT_ESP32-CAM_FIRMWARE_FINAL_BOSS`

Each one holds two .bin files:

| File | Use |
| --- | --- |
| `..._flash_at_0x0.bin` | USB flashing: write it at address 0x0. It erases everything on the board, including saved settings. |
| `<name>.bin` | The app only: for Wi-Fi OTA, or USB at address 0x10000 if you want to keep saved settings. |

If a sketch does not fit the OTA-capable layout, the build switches to Huge APP and prints a warning (USB only, no Wi-Fi OTA).

## Settings used

arduino-cli 1.5.1 and ESP32 core 3.3.5. Main: board `esp32:esp32:esp32`. Camera: board `esp32:esp32:esp32cam`. Partition scheme: Minimal SPIFFS (1.9 MB APP with OTA).
