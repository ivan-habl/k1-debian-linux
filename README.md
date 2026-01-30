# Linux kernel 6.6.18 for the Creality K1 printer series on the Ingenic X2000E processor

## Compilation

```bash
cd ~/work/mips-x2000e/linux

export TOOLCHAIN="$HOME/work/mips-x2000e/mips32r5el--glibc--bleeding-edge-2025.08-1"
export PATH="$TOOLCHAIN/bin:$PATH"
export CROSS_COMPILE=mipsel-linux-
export ARCH=mips

make -j$(nproc) ARCH=$ARCH CROSS_COMPILE=$CROSS_COMPILE
make -j$(nproc) ARCH=$ARCH CROSS_COMPILE=$CROSS_COMPILE uImage

make ARCH=$ARCH CROSS_COMPILE=$CROSS_COMPILE -j$(nproc) modules
make ARCH=$ARCH CROSS_COMPILE=$CROSS_COMPILE INSTALL_MOD_PATH=~/work/mips-x2000e/rootfs modules_install
```

## Kernel load addresses via U-Boot

```bash
In the Tera Term menu
File → Transfer → YMODEM → Send

loady 0x80f00000
bootm 0x80f00000
```

## Writing modules

```bash
tar -czf modules-6.6.18+.tar.gz 6.6.18+
```

```bash
sudo rm -rf /lib/modules/6.6.18+
sudo tar -xvf modules.tar.gz -C /lib/modules/
sudo depmod -a $(uname -r)
```

## Kernel backup

```bash
sudo dd if=/home/printer/uImage of=/dev/mmcblk0p3
```

## Flashing the kernel

Zero out partition p3 and write the new image over the zeros.
Sync afterwards!

```bash
sudo dd if=/dev/zero of=/dev/mmcblk0p3 bs=4K
sudo dd if=/home/printer/uImage of=/dev/mmcblk0p3
sync
```

## Wi‑Fi drivers

- Firmware for the new Wi‑Fi driver is stored in /lib/firmware/brcm/
- The NVRAM for the new driver can be taken from:
  https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/brcm/brcmfmac43430-sdio.AP6212.txt
- The latest firmware can be found at:
  https://github.com/Infineon/ifx-linux-firmware/tree/latest-v5.10/firmware
