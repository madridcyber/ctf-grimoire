# 🏴‍☠️ information

| | |
|---|---|
| **Event / Platform** | picoCTF (picoGym practice) |
| **Category** | Forensics |
| **Difficulty** | ⭐⭐ |
| **Date solved** | 2026-07-26 |

## 🔎 Recon — Reading the Battlefield

The challenge gives a single image file, `cat.jpg`. It opens fine and shows… a cat. Nothing visually hidden, so the flag is likely in the file's **metadata** or a hidden channel, not the pixels.

First reflex — confirm the file type and skim strings:

```bash
file cat.jpg
strings cat.jpg | grep -iE 'pico|flag|CTF' — # nothing obvious
```

`file` reports a normal JPEG, so I move to EXIF/metadata.

## ⚔️ Exploitation — The Killing Move

Dump the metadata with `exiftool`:

```bash
exiftool cat.jpg
```

The interesting field is a suspicious blob in a metadata tag (e.g. **License** / **Copyright**) that looks like Base64 — ends with `=` padding and uses only `[A-Za-z0-9+/]`:

```bash
exiftool -License cat.jpg | awk -F': ' '{print $2}' | base64 -d
```

The decode returns the readable `picoCTF{...}` flag.

**Why it works:** image files carry EXIF/XMP metadata blocks that most viewers ignore. Anything can be stuffed in there — here, a Base64-encoded flag hiding in plain sight.

## 🩸 Flag

```
picoCTF{redacted-solve-it-yourself}
```

## 🧠 Lessons Refined

- **Forensics checklist for images:** `file` → `strings` → `exiftool` → `binwalk`/`zsteg` → pixel-level stego.
- Base64 has a recognisable alphabet and `=` padding — learn to spot it by eye.
- Metadata is a favourite hiding spot; never trust that “just an image” is just an image.
- Keep `exiftool`, `binwalk`, `steghide`, and `zsteg` in the kit.
