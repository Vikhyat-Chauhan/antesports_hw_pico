# Ant Esports 60% Keyboard - Custom QMK Firmware

> Custom QMK firmware for a 60% ANSI mechanical keyboard, reverse-engineered and ported to run on the **RP2040** (Raspberry Pi Pico) microcontroller.

## Overview

This project replaces the stock firmware of an Ant Esports mechanical keyboard with a fully custom **QMK**-based firmware running on the RP2040. The keyboard's key matrix was reverse-engineered to map all 61 keys across a 5x14 GPIO matrix, enabling complete control over keymap behavior, layers, and advanced features not available in the original firmware.

### Key Highlights

- **Reverse-engineered key matrix** &mdash; mapped 61 physical keys to a 5-row x 14-column GPIO matrix via manual circuit tracing
- **Dual-layer keymap** with base QWERTY and a function layer for media, navigation, and system controls
- **Mod-tap keys** &mdash; `RSFT_T(KC_UP)` provides Right Shift on hold, Up Arrow on tap, maximizing a compact layout
- **N-Key Rollover (NKRO)** &mdash; full anti-ghosting for every key combination
- **Media & system controls** &mdash; volume, brightness, playback, and `Ctrl+Alt+Del` on the function layer

## Hardware

| Component | Details |
|---|---|
| **MCU** | RP2040 (Raspberry Pi Pico) |
| **Layout** | 60% ANSI (61 keys) |
| **Matrix** | 5 rows x 14 columns, COL2ROW diode direction |
| **Interface** | USB 2.0 HID |
| **Row Pins** | `GP14` `GP15` `GP16` `GP17` `GP18` |
| **Column Pins** | `GP0` &ndash; `GP13` |

## Keymap Layers

### Layer 0 &mdash; Base

Standard QWERTY with a few optimizations for the 60% form factor:

```
┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───────┐
│Esc│ 1 │ 2 │ 3 │ 4 │ 5 │ 6 │ 7 │ 8 │ 9 │ 0 │ - │ = │ Bksp  │
├───┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─────┤
│ Tab │ Q │ W │ E │ R │ T │ Y │ U │ I │ O │ P │ [ │ ] │  \  │
├─────┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴─────┤
│ Caps │ A │ S │ D │ F │ G │ H │ J │ K │ L │ ; │ ' │ Enter  │
├──────┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬────────┤
│ Shift  │ Z │ X │ C │ V │ B │ N │ M │ , │ . │ / │ Sft/Up │
├────┬───┴┬──┴─┬─┴───┴───┴───┴───┴───┴──┬┴───┬───┴┬──┴─┬──────┤
│Ctrl│Alt │GUI │         Space           │MO1 │ ←  │ ↓  │  →   │
└────┴────┴────┴─────────────────────────┴────┴────┴────┴──────┘
```

- **`QK_GESC`** &mdash; Escape on tap, `` ` `` when shifted
- **`RSFT_T(KC_UP)`** &mdash; Right Shift when held, Up Arrow when tapped
- **`MO(1)`** &mdash; Hold to activate Function layer

### Layer 1 &mdash; Function

```
┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───────┐
│Esc│F1 │F2 │F3 │F4 │F5 │F6 │F7 │F8 │F9 │F10│F11│F12│  Del  │
├───┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─────┤
│     │   │   │   │   │   │   │Prt│Scl│Pse│Mut│Vl-│Vl+│     │
├─────┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴┬──┴─────┤
│      │Exe│Slp│Mai│Sel│Cal│Ins│Hom│PgU│   │Br-│Br+│        │
├──────┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬─┴─┬────────┤
│        │Prv│Nxt│Ply│Stp│Del│End│PgD│   │   │   │ Shift  │
├────┬───┴┬──┴─┬─┴───┴───┴───┴───┴───┴──┬┴───┬───┴┬──┴─┬──────┤
│    │    │Lock│       Ctrl+Alt+Del      │    │ ←  │ ↓  │  →   │
└────┴────┴────┴─────────────────────────┴────┴────┴────┴──────┘
```

**Media:** Play/Pause, Next, Previous, Stop, Volume Up/Down, Mute
**Navigation:** Home, End, Page Up/Down, Insert
**System:** Print Screen, Scroll Lock, Brightness, Sleep, Calculator, `Ctrl+Alt+Del`

## Technical Details

### Matrix Scanning

The keyboard uses **COL2ROW** matrix scanning across 19 GPIO pins. The firmware rapidly drives each row pin low and reads the column pins to detect which keys are pressed, with hardware debouncing to filter contact bounce.

### QMK Features Enabled

| Feature | Purpose |
|---|---|
| **Bootmagic** | Enter bootloader by holding Escape on plug-in |
| **NKRO** | Register unlimited simultaneous key presses |
| **Mousekey** | Emulate mouse input from the keyboard |
| **Extrakey** | Media and system key support via HID |

### Mod-Tap

The `RSFT_T(KC_UP)` key demonstrates QMK's mod-tap feature: the key registers as Right Shift when held past the tapping term threshold, or as Up Arrow when tapped briefly. This technique recovers a dedicated arrow key without sacrificing modifier access on a 60% layout.

## Build & Flash

**Prerequisites:** [QMK build environment](https://docs.qmk.fm/#/getting_started_build_tools)

```bash
# Build firmware
make antesports_hw_pico:default

# Build and flash
make antesports_hw_pico:default:flash

# Or use QMK CLI
qmk flash -kb antesports_hw_pico -km default
```

### Entering Bootloader

| Method | Steps |
|---|---|
| **Bootmagic** | Hold top-left key (Escape) + plug in USB |
| **Physical button** | Press the reset button on the PCB |
| **Keycode** | Press `QK_BOOT` if mapped in your keymap |

## Project Structure

```
antesports_hw_pico/
├── info.json                  # Hardware config: MCU, pins, matrix, layout
├── config.h                   # Firmware feature flags
├── rules.mk                   # Build system rules
├── keymaps/
│   └── default/
│       └── keymap.c           # Keymap layers and key definitions
└── antesports_hw_pico.json    # QMK Configurator export (import-ready)
```

## Skills Demonstrated

- **Embedded systems** &mdash; bare-metal firmware on the RP2040 ARM Cortex-M0+ processor
- **Hardware reverse engineering** &mdash; traced PCB matrix wiring to map GPIO pins to physical keys
- **C programming** &mdash; low-level keymap definitions using QMK's macro system
- **USB HID protocol** &mdash; keyboard communicates as a standard HID device across all operating systems
- **Matrix scanning algorithms** &mdash; efficient multiplexed key detection across a 5x14 grid

## License

MIT License - see [LICENSE](LICENSE) for details.
