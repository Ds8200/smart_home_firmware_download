# Firmware Registry

This folder is the source of truth for all firmware versions.  
The server **never serves the `.bin` files** — the ESP32 downloads them directly from GitHub Raw URLs stored in `index.json`.

## Structure

```
firmware/
  index.json          ← version list with download URLs (managed manually)
  {version}/
    meta.json         ← version metadata and changelog
    firmware.bin      ← compiled binary committed to git
```

## Setup

Set `FIRMWARE_INDEX_URL` in your `.env` to point at the raw GitHub URL of this file:

```
FIRMWARE_INDEX_URL=https://raw.githubusercontent.com/YOUR_USER/YOUR_REPO/main/server/firmware/index.json
```

Also update the `url` field for each version in `index.json` with your actual GitHub username and repo name.

## Adding a New Version

1. Build the firmware in PlatformIO (`pio run -e esp32dev`)
2. Copy `.pio/build/esp32dev/firmware.bin` to `server/firmware/{version}/firmware.bin`
3. Create `server/firmware/{version}/meta.json` with changelog and build info
4. Update `server/firmware/index.json`:
   - Add the new version entry with its `url` pointing to the raw GitHub path
   - Update `"latest"` to the new version
5. Commit and push to GitHub — the ESP32 will download directly from the raw GitHub URL

## URL Format

Each version entry in `index.json` carries its own download URL:
```
https://raw.githubusercontent.com/YOUR_USER/YOUR_REPO/main/server/firmware/{version}/firmware.bin
```

The server fetches `index.json` and `meta.json` from GitHub Raw to serve version metadata to the frontend.  
It does **not** proxy or cache the binary files.
