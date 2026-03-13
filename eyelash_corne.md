ZMK Board - Eyelash Nano
this is a ZMK keyboard controller board based on the nRF52840, named eyelash nano. this repo contains the board definition files and configuration for building ZMK firmware.
board features

MCU: nRF52840 (ARM Cortex-M4F)
connectivity: USB and Bluetooth Low Energy (BLE)
flash partitions:

SD partition: 0x00000000 - 0x00026000
code partition: 0x00026000 - 0x000c6000
storage partition: 0x000ec000 - 0x00008000
boot partition: 0x000f4000 - 0x0000c000


supported peripherals: ADC, USB, Bluetooth, IEEE 802.15.4, PWM, watchdog
GPIO: GPIO0 and GPIO1
interfaces: I2C, SPI, UART
LED: blue LED (GPIO0 15)
power management: supports external power control and battery monitoring

directory structure
├── .github/workflows/           # github actions workflow config
├── boards/arm/eyelash_nano/     # board definition files
│   ├── board.cmake              # board build config
│   ├── eyelash_nano-pinctrl.dtsi    # pin control definitions
│   ├── eyelash_nano.deconfig    # default config
│   ├── eyelash_nano.dts         # device tree source
│   ├── eyelash_nano.dtsi        # device tree include
│   ├── eyelash_nano.yaml        # board metadata
│   ├── eyelash_nano.zmk.yml     # ZMK-specific config
│   ├── Kconfig                  # kconfig options
│   ├── Kconfig.board            # board kconfig definitions
│   ├── Kconfig.defconfig        # default kconfig config
│   └── pre_dt_board.cmake       # pre-processed device tree config
├── config/                      # keyboard config
├── build.yaml                   # github actions build matrix config
└── zephyr/module.yml            # zephyr module config
build instructions
building with github actions
the repo has github actions configured to automatically build firmware when you push code or open a pull request.
the build matrix is defined in build.yaml and currently builds these combinations:
yamlinclude:
  - board: eyelash_nano
    shield: corne_left
  - board: eyelash_nano
    shield: corne_right
you can modify this file to add more board and shield combinations.
building locally
you need to set up a ZMK development environment. see the ZMK docs for details.
configuring west.yml
the repo already has the necessary dependencies in config/west.yml:
yamlmanifest:
  remotes:
    - name: zmkfirmware
      url-base: https://github.com/zmkfirmware
    - name: eyelash
      url-base: https://github.com/a741725193
  projects:
    - name: zmk
      remote: zmkfirmware
      revision: main
      import: app/west.yml
    - name: zmk-board-eyelash
      remote: eyelash
      revision: main
  self:
    path: config
this ensures west can locate the eyelash_nano board definitions.
build steps

clone the ZMK repo

bash   git clone https://github.com/zmkfirmware/zmk.git
   cd zmk

add this repo as a user config

bash   mkdir -p app/boards/shields
   git clone https://github.com/a741725193/zmk-board-eyelash.git config

run west update to fetch all dependencies

bash   west init -l config
   west update

build firmware with eyelash_nano as the target board

bash   west build -b eyelash_nano -d build/corne_left -- -DSHIELD=corne_left
   west build -b eyelash_nano -d build/corne_right -- -DSHIELD=corne_right
troubleshooting
if you get "No board named 'eyelash_nano' found", likely causes are:

west.yml is misconfigured or not loaded correctly
west update was not run
the zmk-board-eyelash repo was not cloned correctly or the path is wrong

fixes:

confirm config/west.yml includes the zmk-board-eyelash project
run west update to ensure all dependencies are present
check that boards/arm/eyelash_nano directory exists
ensure zephyr/module.yml has the correct board_root set

usage
configuring the keyboard
in the config directory you can add or modify keyboard config files. basic corne config is already included:

corne.conf — keyboard config options
corne.keymap — key mapping definitions

flashing firmware
after building, flash using one of these methods:

UF2: put the controller into bootloader mode, then drag and drop the generated .uf2 file onto the drive that appears
nrfjprog: use nRF command line tools to flash

credits:
https://github.com/a741725193/zmk-board-eyelash