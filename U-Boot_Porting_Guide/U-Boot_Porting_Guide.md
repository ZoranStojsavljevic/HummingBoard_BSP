## U-Boot for HummingBoard

Das U-Boot – the Universal Boot Loader: http://www.denx.de/wiki/U-Boot

### Get u-boot

The latest u-boot version might not work out of the box.

	$ git clone http://git.denx.de/u-boot.git u-boot-i.mx6

OR:

	$ git clone git://git.denx.de/u-boot.git u-boot-i.mx6

Then:

	$ cd u-boot-i.mx6

The cloned version will comply with HEAD set to master branch. This
board does need (must) to use older tag!

	$ git checkout v2019.10
	HEAD is now at 61ba1244b5 Prepare v2019.10
	$ git branch
	* (HEAD detached at v2019.10)
	master
	$ git describe
	v2019.10

### Installing arm cross compiler on the host (the host used is Fedora 35 distro)

To install on Debian Stretch/Buster or Ubuntu, the following command
is used:

	$ sudo apt-get install gcc-arm-linux-gnueabihf

To install on Fedora 35 gcc-arm-linux-gnu.x86_64 (which is not native x86_64
compiler), the following command is used:

	$ sudo dnf install gcc-arm-linux-gnu.x86_64

### u-boot Build

Build u-boot using an ARM cross compiler, e.g. Fedora 35 gcc-arm-linux-gnu.x86_64:

To make .config file, the following command is required:

	Debian/Ubuntu:
	$ ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- make -j8 mx6cuboxi_defconfig

	Fedora:
	$ ARCH=arm CROSS_COMPILE=arm-linux-gnu- make -j8 mx6cuboxi_defconfig

### Actual u-boot compilation:

	Debian/Ubuntu:
	$ ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- make -j8

	Fedora:
	$ ARCH=arm CROSS_COMPILE=arm-linux-gnu- make -j8

### [OPTIONAL: PLEASE, SKIP] Actual u-boot compilation for the expert developers

Part of porting effort with no valid .dts tree present:

	Debian/Ubuntu:
	$ ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- make -j8 EXT_DTB=<path/to/your/custom/built/dtb>

	Fedora:
	$ ARCH=arm CROSS_COMPILE=arm-linux-gnu- make -j8 EXT_DTB=<path/to/your/custom/built/dtb>

### Place SPL and u-boot.img OR u-boot.imx on SD card

u-boot.imx will not be generated as this board supports SPL. Two files are
generated: SPL and u-boot.img.

In order to flash the SD card with u-boot components.

Erase partition table/labels on microSD card:

	$ sudo dd if=/dev/zero of=${DISK} bs=1M count=10

Assuming ${DISK} is /dev/sdb :

	$ sudo dd if=/dev/zero of=/dev/sdb bs=1M count=10

SPL should reside at offset 1024KB (1MB) of the SD card. To place it there
(assuming sdX is sdb):

	$ sudo dd if=SPL of=/dev/sdb bs=1k seek=1; sync
	(Note - the SD card node may vary, so adjust this as needed).

	Flash the u-boot.img image into the SD card:
	$ sudo dd if=u-boot.img of=/dev/sdb bs=1k seek=69; sync

For the U-boot.imx case, it should reside at offset 1024KB (1MB) of the SD
card. To place it there:

	$ sudo dd if=u-boot.imx of=/dev/sdb bs=1k seek=1; sync

The SD card device is typically something as /dev/sd<X> or /dev/mmcblk<X>. Note
that there is a need for write permissions on the SD card for the command to
succeed, so there is a need to su - as root, or use sudo, or do a chmod a+w as
root on the SD card device node to grant permissions to users.

To verify if u-boot works, please, insert SD card into the board and try to boot.
It should stop in u-boot monitor with prompt => .

### Create User / partition for the rootfs:

	$ echo "Create primary partition 1 for rootfs"
	$ sudo echo -e "n\np\n1\n\n+16384M\nw\n" | fdisk ${DISK}

	$ echo "Formatting primary partition ${DISK} for rootfs"
	$ sudo mkfs.ext4 -F ${DISK}

Replacing ${DISK} with assumed microSD card's /dev/sdb partition:

	$ echo "Creating a primary partition 1 for rootfs"
	$ sudo echo -e "n\np\n1\n\n+16384M\nw\n" | /sbin/fdisk /dev/sdb

	$ echo "Formatting a primary partition sdb1 for rootfs"
	$ sudo mkfs.ext4 -F /dev/sdb1
