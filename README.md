# STREET RACER — Terminal Port

> A terminal port of **Street Racer** (Atari 2600, 1977) — original by Larry Kaplan.
> Runs entirely in your Linux terminal. No GUI. No dependencies beyond a C compiler and `aplay`.

![banner](banner.gif)
<!-- replace with your box art strip -->

---

## What it is

Street Racer was one of the Atari 2600 launch titles. You drive a car up a single lane, dodging oncoming traffic. Hold fire to accelerate. Don't crash.

This port runs in a standard Linux terminal using ANSI true-colour escape codes and raw terminal input. The sprite data, crash sprite, and speed accumulator algorithm were all reverse-engineered directly from the original 2600 ROM — the same bytes that ran on the hardware in 1977 are rendering your car today.

---

## Requirements

- Linux
- Terminal with **true-colour support** (most modern terminal emulators: Alacritty, Kitty, GNOME Terminal, iTerm2 etc.)
- Terminal window of at least **160 × 48 characters**
- `aplay` for sound — part of `alsa-utils`, usually already installed:
  ```
  sudo apt install alsa-utils      # Debian/Ubuntu
  sudo pacman -S alsa-utils        # Arch
  ```
  Sound is optional — the game runs silently if `aplay` is absent.

---

## Build & run

```bash
gcc -O2 -o strtrcr strtrcr.c
./strtrcr
```

No Makefile, no CMake, no dependencies. One file, one command.

---

## Controls

| Key | Action |
|-----|--------|
| `←` `→` | Steer |
| `f` or `Space` | Accelerate |
| `q` | Quit |

Hold accelerate to build speed. Release to decelerate. The speed curve is taken directly from the original 6502 assembly — same accumulator, same right-shift formula, just scaled for a modern frame rate.

---

## How it works

The port started with a full disassembly of the 2600 ROM (`Street_Racer_NA.a26`, 2048 bytes). Key findings:

- **Sprites** — the car graphics live in a table at ROM address `$F79A`. Eight variants, one per game mode. The player car, crash car, and enemy car are all drawn directly from that table.
- **Crash sprite** — sprite variant 6 from the table: rear wheels intact (`$C3`), front destroyed asymmetrically. Physically accurate — the front takes the hit.
- **Speed algorithm** — zero-page variable `$9E,X` accumulates ±1 per frame (±`SPEED_STEP` scaled for 15fps). Per-frame movement delta = `max(1, speed >> 4)`, giving 1–7 rows per frame. Same formula as the original 6502 code at `$FC7B` (four `LSR` instructions).
- **Rendering** — ANSI true-colour background blocks (`\033[48;2;R;G;Bm`), one terminal cell = one sprite pixel × 2 character widths.
- **Sound** — raw 8-bit PCM piped to `aplay`. Engine: 25%-duty square wave, pitch 40–280 Hz proportional to `speed_acc`. Crash: white noise burst. Score: 880 Hz chirp.

---

## Differences from the original a.k.a. To-do List for v1.1

- Single-player only (original supports up to 4 via driving controllers)
- Keyboard input instead of driving controllers
- One enemy car on screen 
- No game variation selection (difficulty, game modes)
- Approximate sound (TIA chip emulation would require far more)

---

## Credits

**Port:** HYPRC0W (2026)
**Original game:** Larry Kaplan, Atari, 1977

---

## Support

If you enjoyed this, you can [buy me a coffee](https://buymeacoffee.com/YOURLINK).
