## Kernel for HummingBoard

### Partitioning SD card

Two parttion are create: /dev/sdX1 for kernel, and /dev/sdX2 for rootfs.

Please, read the given example where /dev/sdX is /dev/sdb :

	$ echo "Create primary partition 1 for kernel"
	$ echo -e "n\np\n1\n\n+256M\nw\n"  | fdisk /dev/sdb

	$ echo "Create primary partition 2 for rootfs"
	$ echo -e "n\np\n2\n\n+8192M\nw\n"  | fdisk /dev/sdb

	$ echo "Formatting primary partition sdb1 for kernel"
	$ mkfs.vfat -F 32 /dev/sdb1

	$ echo "Formatting primary partition sdb2 for rootfs"
	$ mkfs.ext4 -F /dev/sdb2

### Making kernel using kernel.org vanilla (the latest stable upon writing this document) kernel 5.14.8

The kernel 5.14.8 source code is located @ the following location:

https://mirrors.edge.kernel.org/pub/linux/kernel/v5.x/

The file to be downloaded is the following: linux-5.14.8.tar.xz

The command to unpack the designated kernel is:

	$ tar -xvf linux-5.14.8.tar.xz
	$ cd linux-5.14.8/

To build the kernel, the following should be done:

Make proper .config:

	Debian/Ubuntu:
	$ ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- make -j8 imx_v6_v7_defconfig

	Fedora:
	$ ARCH=arm CROSS_COMPILE=arm-linux-gnu- make -j8 imx_v6_v7_defconfig

Compile the kernel itself:

	Debian/Ubuntu:
	$ ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- make -j8

	Fedora:
	$ ARCH=arm CROSS_COMPILE=arm-linux-gnu- make -j8

The kernel itself to be used is in the directory: .../linux-5.14.8/arch/arm/boot/
and it is called zImage:

	.../linux-5.14.8/arch/arm/boot/zImage

The .dtb file to be used is in the directory: .../linux-5.14.8/arch/arm/boot/dts/
and it is called imx6dl-hummingboard.dtb

	.../linux-5.14.8/arch/arm/boot/dts/imx6dl-hummingboard.dtb

The location on SD card both components should be placed is a /boot directory.
