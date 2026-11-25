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

| Matrix | nRF52840 pin | nice!nano label |
|---|---|---|
| Row 0 | P0.29 | D20 |
| Row 1 | P0.02 | D19 |
| Row 2 | P1.15 | D18 |
| Row 3 | P1.13 | D15 |
| Col 0 | P0.22 | D4 |
| Col 1 | P0.24 | D5 |
| Col 2 | P1.00 | D6 |
| Col 3 | P0.11 | D7 |
| Col 4 | P1.04 | D8 |
| Col 5 | P1.06 | D9 |
| Col 6 | P1.01 | extra pad |
| Col 7 | P1.02 | extra pad |
| Col 8 | P1.07 | extra pad |
| Col 9 | P0.09 | D10 (NFC pin, freed in firmware) |
| Col 10 | P0.10 | D16 (NFC pin, freed in firmware) |
| Col 11 | P1.11 | D14 |

Col 0 is the left-most column. The bottom row only uses columns 0, 4, 5, 6, 7 and 11.

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
  `boards/shields/handwired42/handwired42.dtsi` (`RC(r,11) … RC(r,0)`).
- **Only one column dead** → check that wire / pin in the table above.
- **Studio changes look wrong after editing the keymap file** → in Studio, use
  "Restore Stock Settings" so it reloads the keymap from the firmware.
- **Renaming the keyboard** → change `ZMK_KEYBOARD_NAME` in
  `boards/shields/handwired42/Kconfig.defconfig` (max 15 characters).
