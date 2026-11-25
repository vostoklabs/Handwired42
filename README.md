# ZMK firmware for Handwired42

42-key handwired keyboard, 4 × 12 matrix, nice!nano v2 (or nRF52840 SuperMini clone).
Built the same way as the Diplomat48: ZMK v0.3, Bluetooth + USB, battery reporting,
**deep sleep off**, and **ZMK Studio** support.

## Layout

```
┌─────┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬──────┐
│ Esc │ Q │ W │ E │ R │ T │ Y │ U │ I │ O │ P │ Bksp │
├─────┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼──────┤
│ Tab │ A │ S │ D │ F │ G │ H │ J │ K │ L │ ; │ Ent  │
├─────┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼──────┤
│Shift│ Z │ X │ C │ V │ B │ N │ M │ , │ . │ / │ Del  │
├─────┼───┴───┴───┼───┼───┼───┼───┼───┴───┴───┼──────┤
│Ctrl │           │LWR│Spc│Spc│RSE│           │ Alt  │
└─────┘           └───┴───┴───┴───┘           └──────┘
```

| Layer | How | What's on it |
|---|---|---|
| **Base** | – | QWERTY as above |
| **Lower** | hold LWR | numbers on top row, arrows on H J K L, Home/End, `{ } "`, Print Screen, **Win/GUI** on the Ctrl key |
| **Raise** | hold RSE | `! @ # $ % ^ & * ( )`, `` ` ~ - _ = + [ ] \ \| ' `` |
| **Adjust** | hold LWR + RSE | F1–F12, Bluetooth profiles 1–5, clear profile, media keys, USB/BLE output, **Studio unlock** (Enter key), reset, bootloader (Del key), clear all BT (Ctrl key) |

## Wiring / pins

Diodes are **col2row** (same as the Diplomat).

| Matrix | nRF52840 pin | nice!nano label | Keys (looking at the keyboard) |
|---|---|---|---|
| Row 0 | P0.29 | D20 | Esc … Bksp |
| Row 1 | P0.02 | D19 | Tab … Enter |
| Row 2 | P1.15 | D18 | Shift … Del |
| Row 3 | P1.13 | D15 | Ctrl, thumbs, Alt |
| Col 0 | P0.22 | D4 | right-most: Bksp / Enter / Del / Alt |
| Col 1 | P0.24 | D5 | P / ; / / |
| Col 2 | P1.00 | D6 | O / L / . |
| Col 3 | P0.11 | D7 | I / K / , |
| Col 4 | P1.04 | D8 | U / J / M / Raise |
| Col 5 | P1.06 | D9 | Y / H / N / Space |
| Col 6 | P1.01 | extra pad | T / G / B / Space |
| Col 7 | P1.02 | extra pad | R / F / V / Lower |
| Col 8 | P1.07 | extra pad | E / D / C |
| Col 9 | P0.09 | D10 (NFC pin, freed in firmware) | W / S / X |
| Col 10 | P0.10 | D16 (NFC pin, freed in firmware) | Q / A / Z |
| Col 11 | P1.11 | D14 | left-most: Esc / Tab / Shift / Ctrl |

The columns are wired mirrored (Col 0 is on the right), and the matrix transform
in `handwired42.dtsi` flips them back, same as the Diplomat.

## Getting the firmware

1. Push this folder to a GitHub repo.
2. Open the **Actions** tab, wait for the build, download the `firmware` zip.
3. Double-tap reset on the controller, it mounts as a USB drive.
4. First time only: drop `settings_reset-nice_nano_v2-zmk.uf2` on it, then double-tap reset again.
5. Drop `handwired42.uf2` on it.
6. Pair over Bluetooth as **Handwired42**.

Later updates: Lower + Raise + Del puts the board into the bootloader, so you don't need the reset button.

## ZMK Studio

Open <https://zmk.studio> (Chrome/Edge) or the desktop app, connect over USB or Bluetooth,
then hold **Lower + Raise and press Enter** to unlock. Keys can be remapped live, no reflashing.

## Troubleshooting

- **No keys work at all** → diodes are the other way round. In
  `boards/shields/handwired42/handwired42.overlay` change `diode-direction` to `"row2col"`
  and move `GPIO_PULL_DOWN` from the row pins to the column pins.
- **Keys come out mirrored left↔right** → reverse each row in
  `boards/shields/handwired42/handwired42.dtsi` (already done for this build).
- **One key types two rows at once (e.g. Q also types A), on every column** → the two
  row wires are shorted together. Firmware can't cause this. Check for a solder
  bridge between the two row pins on the controller (Row 0 = D20 and Row 1 = D19 sit
  right next to each other), or two row wires / diode legs touching. With the
  board unplugged, a multimeter on continuity between the two row pins must NOT beep.
- **Only one column dead** → check that wire / pin in the table above.
- **Studio changes look wrong after editing the keymap file** → in Studio, use
  "Restore Stock Settings" so it reloads the keymap from the firmware.
- **Renaming the keyboard** → change `ZMK_KEYBOARD_NAME` in
  `boards/shields/handwired42/Kconfig.defconfig` (max 15 characters).
