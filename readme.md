<picture>
  <source media="(prefers-color-scheme: dark)" srcset="/docs/images/TOTEM_logo_dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="/docs/images/TOTEM_logo_bright.svg">
  <img alt="TOTEM logo font" src="/docs/images/TOTEM_logo_bright.svg">
</picture>

# One layout, several split keyboards

ZMK config for the split keyboards I use daily. Every board runs the **same
layout** — one shared definition in [`config/base.keymap`](config/base.keymap)
(Colemak-DH, timeless homerow mods, combos) with a thin per-board shim that
only maps the physical key arrangement.

Forked from [GEIGEIGEIST/zmk-config-totem](https://github.com/GEIGEIGEIST/zmk-config-totem) —
please use that repo if you are building your own TOTEM. Hardware files and
build guide: [GEIGEIGEIST/totem](https://github.com/GEIGEIGEIST/totem);
QMK config: [qmk-config-totem](https://github.com/GEIGEIGEIST/qmk-config-totem).

## Keyboards

| Board | Keys | MCU | Keymap | Config |
|---|---|---|---|---|
| [TOTEM](https://github.com/GEIGEIGEIST/totem) | 38, column-stagger | Seeed XIAO BLE | [`config/totem.keymap`](config/totem.keymap) | [`config/totem.conf`](config/totem.conf) |
| Corne (choc, wireless) | 42, 6×3+3 | nice!nano v2 | [`config/corne.keymap`](config/corne.keymap) | [`config/corne.conf`](config/corne.conf) |

The TOTEM arrangement (5 alpha columns + 6-key bottom row + 3 thumbs per half)
is a strict subset of the Corne, so the Corne shim just adds the four
outer-pinky keys as dead (`&none`) — both boards feel identical. To use those
keys later, swap an `XXX` placeholder in `config/corne.keymap`.

## Layout

- **Alphas:** Colemak-DH on BASE.
- **Homerow mods:** GACS-style ([urob's timeless homerow mods](https://github.com/urob/zmk-config#timeless-homerow-mods)),
  400 ms tap / 175 ms quick-tap / 150 ms prior-idle.
- **Thumbs:** hold left for NAV, hold right for SYM; NAV + SYM together is ADJ.
- **Layers:** BASE → NAV (arrows, numpad, clipboard) → SYM (symbols, media) → ADJ (function keys, Bluetooth, bootloader).
- **Combos:** 14, identical gestures on every board — caps-word, ESC/BSPC/DEL/TAB/ENTER/`;` on adjacent pairs,
  copy/paste/cut on the bottom row, `-` `_` `=` `` ` `` on vertical same-finger pairs.
  Timings in [`config/combos.dtsi`](config/combos.dtsi).

Board-specific extras (not shared): the Corne enables OLED status screens,
RGB underglow (27 LEDs), and ZMK Studio over USB. If your Corne has no LEDs,
set `CONFIG_ZMK_RGB_UNDERGLOW=n` in `config/corne.conf`.

- **Thumbs:** left is DEL, hold-for-NAV (tap TAB), SPACE; right is RET, hold-for-SYM (tap ESC), BSPC.
  While NAV or SYM is held, the opposite inner thumb becomes ADJ (RET key from NAV, SPACE key from SYM).
- **Layers:** BASE → NAV (arrows, numpad) → SYM (symbols, media) → ADJ (function keys, Bluetooth, bootloader/reset).

![TOTEM layout](/docs/images/TOTEM_layout.svg)

## How to use

- fork this repo
- `git clone` your fork to get a local copy (command line or [GitHub Desktop](https://desktop.github.com/))
- adjust the shared layout in `config/base.keymap` (all keycodes: [zmk.dev/docs/codes](https://zmk.dev/docs/codes/))
- `git push` to your fork
- on your fork's GitHub page go to "Actions", wait for the build, and download the `firmware.zip` artifact
- connect the **left** half to your PC, press reset twice — it appears as a mass-storage device
- drag'n'drop the matching `.uf2` onto it (e.g. `totem_left-xiao_ble__zmk-zmk.uf2`), repeat with the right half

If a half keeps the *old* layout after flashing, stale settings are overriding
the new keymap: flash the matching `settings_reset-…​.uf2` on the left half
first (this also wipes Bluetooth pairings, so re-pair after), then flash the
real firmware again.

## Adding another keyboard

1. Add a thin `config/<board>.keymap`: include the right
   `zmk-helpers/key-labels` header, define `ZMK_BASE_LAYER()` for the physical
   arrangement (see `config/corne.keymap`), then `#include "base.keymap"`.
2. Add `config/<board>.conf` for board settings (name, battery, display…).
3. Add the left/right/settings_reset entries to [`build.yaml`](build.yaml).
