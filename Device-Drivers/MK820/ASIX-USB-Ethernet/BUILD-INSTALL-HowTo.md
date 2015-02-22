<br>
<b> Step 0:  Install Ubuntu Linux 12.04 on miniand:</b>

https://www.miniand.com/forums/forums/2/topics/1?page=23

<br><br>
<b> Step 1: Install Kernel Source - Miniand </b>

See: How to Install Kernel Source:<br>
https://www.miniand.com/forums/forums/2/topics/1?page=19

Install MK802 Linux Kernel Source Code

    sudo apt-get install git
    sudo apt-get install ethtool
    uname -r                        # it shows 3.0.36-t1+
    
    mount /dev/sda1 /media/USB16GB
    export MK802_DIR=/media/USB16GB/mk802    # needs 1.7GB for source.

    cd ${MK802_DIR}
    git clone git://github.com/amery/linux-allwinner.git
    cd linux-allwinner
    git checkout 79e40a1fbd0ee7d624dda59a0f8fbbc984d72f42
    
    # Patch the Makefile and arch/arm/configs/sun4i_defconfig:

    Makefile
    --- a/Makefile
    +++ b/Makefile
    @@ -1,7 +1,7 @@
    VERSION = 3
    PATCHLEVEL = 0
    SUBLEVEL = 36
    -EXTRAVERSION =
    +EXTRAVERSION = -t1
    NAME = Sneaky Weasel

    # DOCUMENTATION

    arch/arm/configs/sun4i_defconfig b/arch/arm/configs/sun4i_defconfig
    --- a/arch/arm/configs/sun4i_defconfig
    +++ b/arch/arm/configs/sun4i_defconfig
    @@ -336,9 +336,9 @@ CONFIG_VMSPLIT_3G=y
    # CONFIG_VMSPLIT_2G is not set
    # CONFIG_VMSPLIT_1G is not set
    CONFIG_PAGE_OFFSET=0xC0000000
    -# CONFIG_PREEMPT_NONE is not set
    +CONFIG_PREEMPT_NONE=y
    # CONFIG_PREEMPT_VOLUNTARY is not set
    -CONFIG_PREEMPT=y
    +CONFIG_PREEMPT=n
    CONFIG_HZ=100
    CONFIG_AEABI=y
    
    # CONFIG_ARCH_SPARSEMEM_DEFAULT is not set
    @@ -1636,6 +1636,7 @@ CONFIG_CLKSRC_MMIO=y
    
     #
     # File systems
     #
    +CONFIG_SQUASHFS=m
     CONFIG_EXT2_FS=y
     # CONFIG_EXT2_FS_XATTR is not set
     # CONFIG_EXT2_FS_XIP is not set
    
    # Create configuration: If necessary, edit arch/arm/configs/sun4i_defconfig to enable more modules.
    make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi- sun4i_defconfig
    
    # Build the kernel modules only: it might fail but it is OK.
    make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi- -j4 modules
    
    # for building module later on:
    ln -s         ${MK802_DIR}/linux-allwinner         /lib/modules/3.0.36-t1+/build
    ln -s         ${MK802_DIR}         /lib/modules/3.0.36-t1+/linux-allwinner


<br><br><br>

<b> Step 2: Install Driver for KY-LAN772AL (USB Ethenet Adapter) </b>
    
Download the driver from below, choose Linux kernel 3.x/2.6.x Driver <br> http://www.asix.com.tw/products.php?op=pItemdetail&PItemID=86;71;101
    
    export ASIX_DIR=/media/USB16GB/asix    
    cd $ASIX_DIR
    make
    
    # make sure asix.ko is copied to somewhere under /lib/modules/3.0.36-t1+/kernel/drivers/net/usb. 
    # If not successful with make install, do it manually, and then do “/sbin/depmod -a”
    # Otherwise modprobe will complain “FATAL: Module asix.ko not found”
    make install     
    
    modinfo asix
    modprobe -f asix    # -f: module symbol missing.
    
Do ifconfig and eth1 will show up. And
    
    root@miniand:/media/usbdisk/mk802# ethtool -i eth0
    driver: wemac
    version: 1.00
    firmware-version:
    bus-info: wemac
    supports-statistics: no
    supports-test: no
    supports-eeprom-access: yes
    supports-register-dump: no
    
    root@miniand:/media/usbdisk/mk802# ethtool -i eth1
    driver: asix
    version: 22-Aug-2005
    firmware-version: ASIX AX88772A USB 2.0 Ethernet
    bus-info: usb-sw-ehci-1.4
    supports-statistics: no
    supports-test: no
    supports-eeprom-access: yes
    supports-register-dump: no
