# Intro

This repository outlines most of the steps needed to build/modify the ZMK firmware for a Wireless Charybdis keyboard.

## Pre Built Firmware

If you'd like to skip all the configuration steps and use something prebuilt, the firmware files for qwerty and colemak dh can be found in the Actions tab of this repo. There are a few things to note about how I've chosen to configure things.

- The main branch builds the firmware with the colemak dh layout, and the layouts/qwerty branch builds the qwerty firmware.
- The keyboard name is Charybdis. This is what will show up when you connect to it with bluetooth.
- ZMK has terms for each side of a split keyboard. Central is the half that sends keyboard outputs over USB or advertises to other devices over bluetooth. Peripheral is the half that will only send keystrokes to the central once they are paired and connected through bluetooth. I have chosen the right side as central because it fits my desk layout better.
- To add support for the PMW3610 sensor, [inorichi's driver](https://github.com/inorichi/zmk-pmw3610-driver?tab=readme-ov-file) is included in the firmware.
- Looking at different options to include support for mouse keys (move and scroll), there are several repos out there. [krikun98](https://github.com/krikun98/zmk/tree/mouse-pr) has one that looked promising, but ultimately I settled on [petejohanson's work](https://github.com/petejohanson/zmk/blob/feat/pointers-move-scroll/docs/docs/behaviors/mouse-emulation.md). This will be included in the prebuilt firmware until ZMK merges it.

## Update Key Mappings

Before adding/updating a keymap, you need to select a behavior, then choose a parameter code. This process is more clearly outlined on ZMK's [Keymaps & Behaviors](https://zmk.dev/docs/features/keymaps) page.
- Behaviors are all documented on the [Behaviors Overview](https://zmk.dev/docs/behaviors)
- Codes are all documented on the [keycodes](https://zmk.dev/docs/codes) page

There are a few options available to update the keymaps with the chosen behavior and code.

### Edit Code Directly

You can edit the charybdis.keymap file directly to match any configuration you want by using the behavior and codes from above.

### Use a GUI

Using a GUI to generate the keymap file content is the easiest option. There are multiple mappers out there that will generate keymap files for ZMK. The two I found most helpful are tammingaj's [Keymapper](https://www.keymapper.dev/code) and nickcoutsos' [keymap editor](https://nickcoutsos.github.io/keymap-editor/). I chose to use the latter for my project.

If you'd like to use the keymap editor, follow the steps below.

- Fork this repo
- Open a new tab to the [keymap editor](https://nickcoutsos.github.io/keymap-editor)
- Give it permission to your fork
- Select the branch you'd like to modify
- Update the keys to match what you'd like to use on your keyboard
- Save
- Wait for the pipeline to run
- Download and flash the new firmware

## Building Your Own Firmware

ZMK provide a comprehensive guide to follow when creating a [New Keyboard Shield](https://zmk.dev/docs/development/new-shield). I'll touch on some of the points here, but their docs should be what you reference when you're building your own firmware.

### File Locations

The file locations for setting up the repo to build the firmware matters. The formats and locations of the files can be found on ZMK's [Configuration Overview](https://zmk.dev/docs/config). One thing to note about this repo is that the keymap and json files are in the config directory to work with a GUI keymapper. Also, the matrix-transform is included as part of the charbdis.dtsi file.

### Mapping GPIO Pins to Keys

To set up some of the configuration files it requires a knowledge of which keys connect to which pins on the MCU (see the [Shield Overlays](https://zmk.dev/docs/development/new-shield#shield-overlays) section), and how the rows and columns are wired.

To get this information, look at the PCB kcad files and follow the traces from key pads, to row and column through holes, to MCU through holes. Once you have that information you can update the shield.dtsi, shield_left.overlay, and shield_right.overlay files.

### Changing the Central and Peripheral Assignments

Follow the ZMK documentation to change the [Kconfig.deconfig](https://zmk.dev/docs/development/new-shield#kconfigdefconfig).

### Changing the Keyboard Name

Follow the ZMK [Kconfig.defconfig](https://zmk.dev/docs/development/new-shield#kconfigdefconfig) section to update the keyboard name. Make sure to read about the danger in exceeding the 16 character limit.

## Creating Graphical Key Maps
 
There are a lot of resources available to create key map images. Keymap Editor and KeyMapper mentioned in the Update Keymappings section have this ability, but they are not very customizable.

[Keymap Drawer](https://keymap-drawer.streamlit.app/) has more options and is able to process ZMK keymaps, but I ultimately settled on the [Keyboard Layout Editor](http://www.keyboard-layout-editor.com/#/).
 
## Upcoming Features

ZMK is actively being developed and there are a few features I'll be adding to my builds as soon as they are released.

- Mouse Pointer & Scrolling - [PR in review](https://github.com/zmkfirmware/zmk/pull/2027)
- Layer Locks - [Layer locks will become standard soon.](https://github.com/zmkfirmware/zmk/pull/1984)