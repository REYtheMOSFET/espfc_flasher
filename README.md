# ESP-FC Flasher

A browser flasher for [esp-fc](https://github.com/rtlopez/esp-fc) (ESP32 flight-controller firmware). Pick a dev kit, flash it over USB with Web Serial, then configure it in **Betaflight Configurator 10.10**.

**Live flasher: https://reythemosfet.github.io/espfc_flasher/**

Supported chips: ESP32, ESP32-S3, ESP32-S2 (experimental), ESP32-C3 (experimental), ESP8266 (obsolete). Works in desktop Chrome or Edge.

## How it works

1. Pick your dev kit, describe your hardware (gyro bus, receiver, ESC protocol, GPS), and the page generates a pin and ESC setup script.
2. The page finds the matching firmware for your chip, hosted next to it and checked against a SHA-256 checksum. It flashes the board over USB after confirming the connected chip matches your choice.
3. Connect with Betaflight Configurator 10.10 and paste in the setup script.

## Set up your own copy

1. Fork or copy this repo (`index.html`, `README.md`, `.github/workflows/pages.yml`).
2. In the repo, go to **Settings → Pages** and set **Source** to **GitHub Actions**.
3. Open **Actions → Build flasher site → Run workflow**. It downloads the latest esp-fc release binaries, writes `firmware/manifest.json` with SHA-256 checksums, and deploys to `https://<your-username>.github.io/<repo-name>/`.

The workflow re-runs weekly and on every push to `main`, so new esp-fc releases show up on their own.

## Custom boards and pin defaults

esp-fc takes its default pins from `lib/Espfc/src/Target/Target<chip>.h`. To ship your own defaults (a custom flight-controller PCB, say):

1. Fork `rtlopez/esp-fc` and edit the target header for your chip.
2. Run the workflow with **source = build** and **repo = `<your-username>/esp-fc`** (optionally a branch or tag in **ref**).
3. The workflow compiles all targets with PlatformIO and publishes them. Same page, your firmware.

## Run it locally

Web Serial needs a secure context, so serve the folder instead of double-clicking the file:

```
python3 -m http.server 8000
```

then open `http://localhost:8000`. With no `firmware/manifest.json` present, the page asks you to drop in a `.bin` downloaded from the esp-fc Releases page.

## ESC flashing

The flight controller has to bridge the serial lines to the ESCs. esp-fc has an experimental BLHeli 4-way passthrough (since v0.2.0-RC4). Step 5 on the page explains how to use [esc-configurator.com](https://esc-configurator.com) through it. Confirm the exact ESC model before writing any ESC firmware.

## Credits and licenses

- **esp-fc** firmware: © 2016 rtlopez and contributors, MIT License. The MIT license requires its copyright and permission notice to ship with every copy, so the workflow publishes it at `firmware/ESP-FC-LICENSE.txt` next to the binaries. Don't remove that step or the credits footer in `index.html`.
- **esptool-js** (flashing): Espressif, Apache-2.0. The page loads it from a CDN, so it isn't redistributed here. If you vendor it into the repo, include its license and NOTICE.
- **ESC Configurator** and **Betaflight Configurator**: linked only, not bundled.
- The page code in this repo is by REYtheMOSFET. Add a `LICENSE` file to set its terms; that doesn't change the licenses above.
- This flasher is unofficial and not endorsed by any of the projects above.

## Notes

- The page flashes the **merged** image (`..._0x00.bin`) at address `0x00`. ESP8266 uses the plain `.bin`, also at `0x00`.
- It refuses to flash if the connected chip doesn't match the selected board.
- Not yet tested on real hardware. Try it on a spare board first, and test motors with propellers off.
- Provided as is, without warranty.
