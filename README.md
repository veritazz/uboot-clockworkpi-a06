# uboot-clockworkpi-a06

u-boot for ClockworkPI A06 w/ rockchip blobs/miniloader

Based on PKGBUILD for pinebookpro: https://gitlab.manjaro.org/manjaro-arm/packages/core/uboot-pinebookpro-bsp

## Build `idbloader.img`

```bash
git clone https://github.com/rockchip-linux/rkbin.git
rkbin/tools/mkimage -n rk3399 -T rksd -d rkbin/bin/rk33/rk3399_ddr_800MHz_v1.25.bin idbloader.img
cat rkbin/bin/rk33/rk3399_miniloader_v1.26.bin >> idbloader.img
```

## Build `trust.img`

```bash
git clone https://github.com/rockchip-linux/rkbin.git
cd rkbin/
tools/trust_merger RKTRUST/RK3399TRUST.ini
mv trust.img ../
```

## Build `u-boot-dtb.bin`

```bash
# Requires base-devel, bc, dtc, aarch64-linux-gnu-gcc installed
export CROSS_COMPILE="ccache aarch64-linux-gnu-"

wget https://ftp.denx.de/pub/u-boot/u-boot-2021.10.tar.bz2
tar -xf u-boot-2021.10.tar.bz2
cd u-boot-2021.10
patch -Np1 -i ../0001-uboot-clockworkpi-a06.patch
make clockworkpi-a06-rk3399_defconfig
echo 'CONFIG_IDENT_STRING=" Manjaro ARM"' >> .config
make EXTRAVERSION=-${pkgrel} all
```

## Build `uboot.img` from `u-boot-dtb.bin`

```bash
git clone https://github.com/rockchip-linux/rkbin.git
rkbin/tools/loaderimage --pack --uboot u-boot-2021.10/u-boot-dtb.bin uboot.img 0x200000
```