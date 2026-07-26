# 🏴‍☠️ Mod 26

| | |
|---|---|
| **Event / Platform** | picoCTF (picoGym practice) |
| **Category** | Crypto |
| **Difficulty** | ⭐ |
| **Date solved** | 2026-07-26 |

## 🔎 Recon — Reading the Battlefield

The challenge hands over a single ciphertext string that *looks* like a flag with its letters scrambled:

```
cvpbPGS{arkg_gvzr_V'yy_gel_ebg13_uggcf://ebg13.pbz/}
```

Two tells jump out:

- The `{ ... }` structure and the leading `cvpbPGS` scream **picoCTF** with the letters rotated.
- The title, *Mod 26*, points straight at a rotation cipher over the 26-letter alphabet. “Mod 26” + obvious flag shape = **ROT13** (its own inverse).

## ⚔️ Exploitation — The Killing Move

ROT13 is a Caesar shift of 13, so decoding is just applying ROT13 again. One-liner:

```bash
echo "cvpbPGS{...}" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

Or with my own packet-paws tool (shift 13 either direction works for ROT13):

```bash
python3 ../packet-paws/crypto/caesar_claw.py -s 13 "cvpbPGS{...}"
```

Both spit out the readable `picoCTF{...}` flag instantly.

**Why it works:** ROT13 maps each letter 13 positions forward; since 13 + 13 = 26 ≡ 0 (mod 26), running it twice returns the original. It provides zero security — it's obfuscation, not encryption.

## 🩸 Flag

```
picoCTF{redacted-solve-it-yourself}
```

## 🧠 Lessons Refined

- **Recognise the shape before you reach for tools.** A `{}`-wrapped string with a mangled prefix is almost always a rotated flag.
- ROT13 is self-inverse — encode == decode.
- When a title names the math (“Mod 26”), it's telling you the algorithm. Read the flavour text.
- `tr` is the fastest ROT13 on any Unix box; keep it in muscle memory.
