# Elxie Flasher — Test Build

A ready-to-host test of the Elxie web flasher, wired to five RGB test firmwares
so you can *see* flashing work: each dropdown version flashes a different LED
behavior on the board.

## Step 1 — Rename your 5 bin files

Rename each of your RGB test builds to the matching version name, then put them
in the `firmware/` folder (replacing the placeholder files there):

| Your file            | Rename to           | Dropdown shows        |
|----------------------|---------------------|-----------------------|
| `RGBwith500ms.bin`   | `elxie-v1.2.0.bin`  | v1.2.0 · blink 500ms  |
| `RGBwith100ms.bin`   | `elxie-v1.1.3.bin`  | v1.1.3 · blink 100ms  |
| `RGB_Red.bin`        | `elxie-v1.1.0.bin`  | v1.1.0 · solid red    |
| `RGB_Green.bin`      | `elxie-v1.0.2.bin`  | v1.0.2 · solid green  |
| `RGB_Blue.bin`       | `elxie-v1.0.0.bin`  | v1.0.0 · solid blue   |

(If you'd rather not rename, tell me and I'll relabel the versions instead.)

## Step 2 — Host it

1. Create a new GitHub repo.
2. Upload everything: `index.html`, `versions.json`, the `manifests/` folder,
   and the `firmware/` folder (with your renamed real bins).
3. Settings → Pages → branch `main`, folder `/ (root)` → Save.
4. Open the live URL in **Chrome or Edge**.

## Step 3 — Flash

Plug the board in with a **USB data cable**, pick a version, click Connect &
Flash, choose the port in the popup, and watch the LED change.

Flip between v1.0.0 (blue) and v1.0.2 (green) to prove flashing really works.

## What's fixed in this build

- The flashing library now loads from **jsDelivr** (ad blockers rarely block it),
  not unpkg.
- If the library is ever blocked anyway, the page now **says so clearly**
  instead of the button doing nothing. No more silent dead button.
- Note: on your own computer, turn off Brave Shields / ad blockers for the site
  to be safe.

## Note on chipFamily

The manifests say `chipFamily: "ESP32-S3"` — that's required by the flashing
engine to work, and is never shown in the UI.
