# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a ZMK firmware configuration for the TOTEM split keyboard - a 38-key column-staggered split keyboard designed for the SEEED XIAO BLE microcontroller. The project uses ZMK (Zephyr-based Modern Keyboard firmware) and builds firmware via GitHub Actions.

## Building Firmware

Firmware is built automatically via GitHub Actions whenever changes are pushed:

1. Modify the keymap in `config/totem.keymap`
2. Push changes to GitHub
3. Navigate to the "Actions" tab on GitHub
4. Download the `firmware.zip` archive from the latest successful build
5. Extract `.uf2` files for left and right halves

The build configuration is defined in:
- `build.yaml`: Specifies board (`seeeduino_xiao_ble`) and shields (`totem_left`, `totem_right`)
- `.github/workflows/build.yml`: Triggers the ZMK build workflow

## Keymap Architecture

The keymap is located at `config/totem.keymap` and defines 6 layers:

1. **BASE (0)**: Colemak-DH layout with home row mods (GACS modifiers on left, SCAG on right)
2. **NAV (1)**: Navigation, arrows, brackets, and numpad
3. **SYM (2)**: Symbols, special characters (including German umlauts), media controls
4. **ADJ (3)**: Adjustment layer for system functions (reset, bootloader, Bluetooth, function keys)
5. **TVP1 (4)**: TVPaint layer 1 with custom shortcuts for animation software
6. **TVP2 (5)**: TVPaint layer 2 with additional animation shortcuts

### Layer Access

- BASE layer: Default layer
- NAV layer: Hold TAB on base layer (left thumb, position 4)
- SYM layer: Hold ESC on base layer (right thumb, position 5)
- ADJ layer: Accessible via momentary hold from NAV or SYM layers
- TVP1 layer: Toggle via combo (keys 11+12+13 simultaneously)
- TVP2 layer: Hold L key while in TVP1 layer

### Combos

- **Escape**: Keys 0+1 (Q+W) with 50ms timeout
- **TVP Toggle**: Keys 11+12+13 (Z+X+C) with 100ms timeout

### Macros

- **gif**: Types "#gif" (used for Slack/chat integrations)

### Home Row Mods Configuration

The mod-tap behavior is customized in the keymap:
- `tapping-term-ms`: 170ms
- `quick-tap-ms`: 100ms
- `flavor`: "tap-preferred"
- `global-quick-tap`: enabled

## Hardware Configuration

### Shield Definition

The keyboard is defined as a split shield in `config/boards/shields/totem/`:

- **totem.dtsi**: Matrix configuration (4 rows × 10 columns, col2row diodes)
- **totem_left.overlay**: Left half GPIO mapping (columns 4,5,10,9,8)
- **totem_right.overlay**: Right half GPIO mapping (columns 8,9,10,5,4 with col-offset 5)
- **Kconfig.defconfig**: Sets keyboard name to "TOTEM" and configures split role
- **Kconfig.shield**: Defines left/right shield configs

### Matrix Layout

The keyboard uses a 38-key layout (19 per side):
- Top row: 5 keys per side
- Home row: 5 keys per side
- Bottom row: 6 keys per side (includes outer pinky columns)
- Thumb cluster: 3 keys per side

Row GPIOs: xiao_d pins 0, 1, 2, 3
Column GPIOs vary by side (see overlay files)

## ZMK Configuration

- **west.yml**: Points to ZMK main repository for firmware dependencies
- **totem.conf**: Global config (currently only disables USB logging)
- **totem_left.conf** and **totem_right.conf**: Side-specific configs (currently empty)

## Modifying the Keymap

When editing keymaps:

1. Use ZMK keycodes documented at https://zmk.dev/docs/codes/
2. The keymap has visual ASCII diagrams showing the layout for each layer
3. Bindings follow the physical layout from top-left to bottom-right
4. Layer-tap behaviors use `&lt LAYER KEY` syntax
5. Mod-tap behaviors use `&mt MODIFIER KEY` syntax
6. Special characters (umlauts) use Right Alt combinations: `&kp RA(A)` for Ä, `&kp RA(O)` for Ö, `&kp RA(U)` for Ü, `&kp RA(S)` for ß

## Flashing Firmware

To flash the compiled firmware to the keyboard:

1. Connect one half to the computer via USB
2. Double-tap the reset button
3. The keyboard will appear as a mass storage device
4. Drag and drop the corresponding `.uf2` file (`totem_left-seeeduino_xiao_ble-zmk.uf2` or `totem_right-seeeduino_xiao_ble-zmk.uf2`)
5. The device will automatically reboot with the new firmware
6. Repeat for the other half

## Special Features

- **Bluetooth**: The keyboard supports Bluetooth connectivity via the XIAO BLE
- **Split Communication**: Left half acts as central (master), right half as peripheral
- **TVPaint Integration**: Layers 4-5 provide specialized shortcuts for TVPaint animation software using function key combinations (F11-F20)
