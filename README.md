# Elxie Web Installer

A browser-based firmware flasher for Elxie. Users plug the robot in over USB,
pick a firmware version, and click flash — no drivers, no command line, nothing
to install. Flashing runs entirely in the browser over Web Serial using
[ESP Web Tools](https://esphome.github.io/esp-web-tools/).

## What's in here

```
index.html                 The flasher page (UI + flashing logic)
versions.json              The list that drives the version dropdown
manifests/                 One manifest per firmware version
  elxie-v1.2.0.json
  ...
firmware/                  The flashable .bin files (PLACEHOLDERS — replace these)
  elxie-v1.2.0.bin
  ...
```

## Requirements

- The page **must be served over HTTPS** (or `http://localhost` for testing).
  Web Serial refuses to run otherwise.
- Users must open it in **Chrome, Edge, or Opera on desktop**. Firefox, Safari,
  and all mobile browsers don't support Web Serial — the page detects this and
  shows a friendly notice.

## Try it locally

```bash
cd elxie-flasher
python3 -m http.server 8000
# open http://localhost:8000
```

Real flashing needs real firmware (see below). With the placeholder .bin files
the UI loads and the dropdown works, but a flash attempt will fail — that's
expected until you drop in real binaries.

## Adding real firmware

1. **Build your firmware**, then merge the output into a single binary:

   ```bash
   esptool.py --chip esp32s3 merge_bin -o elxie-v1.2.0.bin @flash_args
   ```

   A single merged `.bin` at offset `0` is the simplest thing to manage. (If you
   prefer separate bootloader / partition / app parts, list them all under
   `parts` in the manifest with their offsets instead.)

2. **Drop it in** `firmware/` replacing the matching placeholder.

## Releasing a new version

Three small steps, no code changes:

1. Add the merged bin: `firmware/elxie-v1.3.0.bin`
2. Add a manifest: copy an existing one in `manifests/` and update the `version`
   and the bin `path`.
3. Add one line to the **top** of the `versions` array in `versions.json`:

   ```json
   { "name": "v1.3.0", "channel": "stable", "note": "latest",
     "manifest": "manifests/elxie-v1.3.0.json" }
   ```

The dropdown rebuilds itself from `versions.json` on every page load. Keep the
newest version first — it's the default selection.

`channel` and `note` are optional labels shown after the version name
(e.g. `v1.3.0 · stable · latest`).

## Deploying

Any static host with HTTPS works. GitHub Pages is the common choice:

1. Push this folder to a repo.
2. Settings → Pages → deploy from `main` (root).
3. Your flasher is live at `https://<user>.github.io/<repo>/`.

Netlify, Cloudflare Pages, and Vercel work the same way — point them at this
folder and you're done.

## How it fits together

- `index.html` fetches `versions.json` and builds the dropdown.
- Selecting a version points a hidden `<esp-web-install-button>` at that
  version's manifest.
- The styled "Connect & Flash" button clicks that hidden button, which opens the
  browser's device picker and runs the flash.
- ESP Web Tools emits `state-changed` events (preparing / erasing / writing /
  finished / error); the page mirrors those onto Elxie's face animations and the
  status pill.

## Notes

- The manifest's `chipFamily` (`ESP32-S3`) is required by ESP Web Tools to flash
  correctly. It is never shown in the UI.
- Nothing is uploaded anywhere — flashing is a direct, local USB operation
  between the browser and the robot.
