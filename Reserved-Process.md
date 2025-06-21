# Process

ARM BROADCOM BCM283X / BCM27XX

M:    Matthias Brugger <mbrugger@suse.com>

M:    Peter Robinson <pbrobinson@gmail.com>

S:    Maintained

F:    arch/arm/dts/bcm283*

F:    arch/arm/mach-bcm283x/

F:    board/raspberrypi/

F:    drivers/gpio/bcm2835_gpio.c

F:    drivers/mmc/bcm2835_sdhci.c

F:    drivers/mmc/bcm2835_sdhost.c

F:    drivers/serial/serial_bcm283x_mu.c

F:    drivers/serial/serial_bcm283x_pl011.c

F:    drivers/video/bcm2835.c

F:    include/dm/platform_data/serial_bcm283x_mu.h

F:    include/dt-bindings/pinctrl/bcm2835.h

F:    drivers/pinctrl/broadcom/

F:    configs/rpi_*

T:    git https://source.denx.de/u-boot/custodians/u-boot-arm.git

---

1.

git clone --branch='u-boot-2023.07.y'  [https://github.com/u-boot/u-boot.git](https://github.com/u-boot/u-boot.git)

2.

Tạo thư mục:

**u-boot/board/ngxxfus/nfus**

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfHxPvjN_dZFxcFEbEWXKpaojSiPAqZEUgLS8MPvvr2UKwt18WZ7oZM-x-hD8ZZOJaUFP2_OkykPjzsDOsUv4BuNWkaRY0amnsMglHPfs_jekPhThLba-MliDgFVKuuQN6LRhG6GQ?key=L_Gjf62Qg3JKXWpL6xNacw)

Hiệu chỉnh tên:

raspberrypi		→		ngxxfusfus

raspberry 		→		ngxxfus

broadcom		→		fakebroadcom

brcm			→		nfs

bcm			→		nf

rpi			→		nfus

RPi			→		NFus

Sửa Makefile:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfRiZ51sV3YfkYG1iH-GNvgM7dXxRKtNtouj4zorjadV1oKQsIkQYj2cpJepStEpSe76X__zg-SkJIm24PjuuDs_ymlnkl0Tm6iiI-jfr0TCoMKqc39YYWKyOnzXlploSKg6rdgkg?key=L_Gjf62Qg3JKXWpL6xNacw)

Suawr ../Makefile

/home/ngxxfus/Desktop/mod3_uboot/arch/arm/Makefile

/home/ngxxfus/Desktop/mod3_uboot/arch/arm/dts/Makefile

3.

Dựa theo danh sách headers, truy vết ngược, tạo file:

**uboot/include/dm/platform_data/serial_nf283x_mu.h**

4.

Tạo một máy mới dựa trên Arm

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXefRBPw1RfdyQUvqHtlRibPT7jqMvdsmTjr1zraf_3dFg3-mf8TiCXoG9KL-ffqYEe5XpzhI65nYd9Z9sbA5OxKUICjChYl9wc845R6aIlFqavTnh_963lcrOY2mDVYELrdcyGhzg?key=L_Gjf62Qg3JKXWpL6xNacw)

Sửa Kconfig:

**uboot/arch/arm/Kconfig**

5.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXd3wZtBx6f4Rm4j8_43Mzk3ayRX0v92-VeKmfXOpRyAJw8f5KNc4P0-JmeFMlt5Kve9CKH11L_F47efJvwdUjuGekyOLuHC2vguvpN_amsGKB5fsy32v4waTt_xa7OMnPAZ3XWIRA?key=L_Gjf62Qg3JKXWpL6xNacw)

6.

Thêm defconfig

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXceUHKfrt-PgOPpLuv2hK7ZlPc_uf9dXzRFRktl7R_8mI1G88Ubc015TW4OECqfvJTRXS8MFtvgv9QVohhlzZSFC0wjeYYG34EMk4e7eYu7D63uOtR_pWF-DUlNisBX-gVLNJhVOw?key=L_Gjf62Qg3JKXWpL6xNacw)

Truy vết, hiệu chỉnh các file:

uboot/drivers/net/Kconfig

uboot/drivers/net/nfgenet.c

uboot/drivers/net/Makefile

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXebbmK6ZAJbPSKMlWmFIGX8TN2VguM11A33gkcb1Ap5xMTJAF_q_Oh32Fz2EsdzIw4OvT81FMstKUel047zMPKwN1nfHdVTIJskW132xCKAMfGUyEncw3nxtTQuTfo2B6eGIMUJEA?key=L_Gjf62Qg3JKXWpL6xNacw)

7.

Hiệu chỉnh driver dựa vào Makefile

uboot/drivers/gpio/nf2835_gpio.c

uboot/drivers/gpio/Makefile

uboot/drivers/mmc/nf2835_sdhci.c

uboot/drivers/mmc/nf2835_sdhost.c

uboot/drivers/pinctrl/fakebroadcom/

uboot/drivers/pinctrl/Makefile

uboot/drivers/mmc/nf2835_sdhost.c

uboot/drivers/serial/serial_nf283x_pl011.c

uboot/drivers/serial/Makefile

uboot/drivers/tee/fakebroadcom/

uboot/drivers/tee/Makefile

uboot/drivers/video/nf2835.c

uboot/drivers/video/Makefile

uboot/drivers/watchdog/nf2835_wdt.c

uboot/drivers/watchdog/Kconfig

uboot/drivers/watchdog/Makefile

uboot/drivers/mmc/Kconfig

uboot/drivers/pci/pcie_nfsstb.c

uboot/lib/efi_loader/Kconfig

uboot/arch/arm/cpu/armv8/Kconfig

Truy vết và sửa các file headers:

uboot/include/dt-bindings/soc/nf2835-pm.h

uboot/include/dt-bindings/clock/nf2835-aux.h

uboot/include/dt-bindings/clock/nf2835.h

uboot/include/dt-bindings/pinctrl/nf2835.h

uboot/include/dm/platform_data/serial_nf283x_mu.h

uboot/include/configs/nfus.h

8.

Sửa device-tree

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXczcTF2QzolaOl49gxBc7Nd5konbLRorPKG9ypcxPuc67IIdBqe82FRvNtwwmFxdSMZ6Zko50dk16ntinlTecvEq34pM8A_oSktHbrpM79NUHy3ERfdHcwKrYJsiIyx1mY8mv42LA?key=L_Gjf62Qg3JKXWpL6xNacw)

Truy vết và sửa các file:

uboot/arch/arm/dts/include/dt-bindings/power/ngxfusfus-power.h