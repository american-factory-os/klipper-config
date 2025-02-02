How to build Klipper on Ubuntu using [dw-0/kiauh](https://github.com/dw-0/kiauh.git).

## System requirements
1. Install `gcc-avr`
    ```bash
    sudo apt-get update
    sudo apt-get -y install gcc-avr
    ```
2. Install Arm GNU toolchain (via these [instructions](https://lindevs.com/install-arm-gnu-toolchain-on-ubuntu
))
    ```bash
    ARM_TOOLCHAIN_VERSION=$(curl -s https://developer.arm.com/downloads/-/arm-gnu-toolchain-downloads | grep -Po '<h4>Version \K.+(?=</h4>)')
    echo $ARM_TOOLCHAIN_VERSION

    curl -Lo gcc-arm-none-eabi.tar.xz "https://developer.arm.com/-/media/Files/downloads/gnu/${ARM_TOOLCHAIN_VERSION}/binrel/arm-gnu-toolchain-${ARM_TOOLCHAIN_VERSION}-x86_64-arm-none-eabi.tar.xz"

    sudo mkdir /opt/gcc-arm-none-eabi
    sudo tar xf gcc-arm-none-eabi.tar.xz --strip-components=1 -C /opt/gcc-arm-none-eabi
    ```
1. Add `arm-none-eabi-gcc` to your shell config, e.g. `~/.zshrc`
    ```bash
    # Needed to build klipper
    export PATH=$PATH:/opt/gcc-arm-none-eabi/bin
    ```

## Build firmware with kiauh

Get **kiauh**:
```bash
# kiauh expects Klipper installed in this dir
mkdir -p /home/$(whoami)/klipper
git clone https://github.com/Klipper3d/klipper.git /home/$(whoami)/klipper

# install kiauh
git clone https://github.com/dw-0/kiauh.git
cd kiauh
./kiauh.sh
```

On the **kiaua** menu select:
1. 4 - advanced
2. 1 - Build

Use these configurations for `Bigtree SKR 1.4` board:
* Microcontroller Architecture: `LPC176x`
* Processor model:
  * `lpc1768` for a standard SKR 1.4 board
  * `lpc1769` for an SKR 1.4 Turbo board
* Bootloader offset: `16KiB`
* Clock Reference: `12 MHz`
* Communication Interface: `USB` (unless you have a specific reason to use UART...)

Confirm build
```bash
ls -l /home/$(whoami)/klipper/out
```

Install firmware
1. Copy `.bin` file to SD card
   ```bash
   cp /home/$(whoami)/klipper/out/klipper/bin /media/<your SD card>
2. Reboot the board
3. Make sure the klipper device is seen by the OS:
  ```bash
  ls /dev/serial/by-id
  ```

