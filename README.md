# defcon25-badge
Badge Related items for defcon 25

Cloned from https://github.com/thedarknet/defcon25-badge.  I just wanted to add some extra instructions for myself
(and others) on how to program the badge with firmware.

# Assembly

Assembly instructions are located here:

https://dcdark.net/2017/badge/index.html

# Flashing firmware (Linux)

Download some prerequisites.  I'm sure this list is fairly incomplete since I had a bunch of software development
tools installed before I played around with this.

    sudo apt-get install cmake build-essential git libgtk-3-dev libusb-1.0 libusb-dev

    # I also installed the following, but I don't think it was actually needed
    sudo apt-get install libusb++-dev

Use git to clone the st-link programming software

    mkdir checkouts
    cd checkouts
    git clone https://github.com/texane/stlink
    git clone https://github.com/mwales/defcon25-badge

Build the stlink software

    cd stlink
    mkdir build
    cd build
    cmake ../
    make

The software is now built, you will have to run it as sudo.  I used the command "sudo make install" as well to copy
into my local system, but that probably was unnecessary.    

Connect the badge to the ST-LINK V2 USB programmer.  You don't need to connect any Vcc connection on the programming
connector if you simply plug the badge into USB and give it voltage that way.  There are 2 different voltages on the
USB programmer, and I'm not even sure which one it is supposed to use since I used the USB power instead.

Connect the SWDIO, GND, and SWCLK pins from the programmer to the header on the programming connection on the board.
Plug the USB port into your computer, and flip the badge power switch to on.  Shamefully, I forgot to do this, I
thought the board was on since an LED was on (not realizing it was only the charging LED).  You can't program it with
the board off.

You can probe for the board with the following command:

    sudo ./st-info --probe

My device and badge returned the following information:

```
Found 1 stlink programmers
 serial: 303030303030303030303031
openocd: "\x30\x30\x30\x30\x30\x30\x30\x30\x30\x30\x30\x31"
  flash: 131072 (pagesize: 2048)
   sram: 40960
 chipid: 0x0422
  descr: F3 device
```

Finally, execute the st-link software and have it flash the firmware on the badge:

    sudo ./st-flash --reset --format ihex write ../../defcon25-badge/software/firmware/bin/firmware.hex

st-flash output the following:

```
st-flash 1.4.0-36-g0af68c0
2018-05-09T01:38:07 INFO common.c: Loading device parameters....
2018-05-09T01:38:07 INFO common.c: Device connected is: F3 device, id 0x10036422
2018-05-09T01:38:07 INFO common.c: SRAM size: 0xa000 bytes (40 KiB), Flash: 0x20000 bytes (128 KiB) in pages of 2048 bytes
2018-05-09T01:38:07 INFO common.c: Attempting to write 93840 (0x16e90) bytes to stm32 address: 134217728 (0x8000000)
Flash page at addr: 0x08016800 erased
2018-05-09T01:38:08 INFO common.c: Finished erasing 46 pages of 2048 (0x800) bytes
2018-05-09T01:38:08 INFO common.c: Starting Flash write for VL/F0/F3/F1_XL core id
2018-05-09T01:38:08 INFO flash_loader.c: Successfully loaded flash loader in sram
 46/46 pages written
2018-05-09T01:38:13 INFO common.c: Starting verification of write complete
2018-05-09T01:38:15 INFO common.c: Flash written and verified! jolly good!
```

# Debugging Environment / Original Instructions

Installation of IDE:
Follow the following instructions (step by step):  

http://gnuarmeclipse.github.io/install/

	Notes: on Window's have gotten both Mars and Luna to work
	on linux i've only gotten luna to work (on ubuntu 16.04 LTS)

Once eclipse is set up here's what the debug config needs to look like, notes the 2 -f openocd scripts that must be configured.

For 2017 development we are using this nucleo board, while it is not the exact MCU we plan to use it is close and the one we want to use does not have a dev kit.

http://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mcu-eval-tools/stm32-mcu-eval-tools/stm32-mcu-nucleo/nucleo-f302r8.html

![alt text](https://github.com/thedarknet/defcon25-badge/blob/master/Debug-ScreenShot.png "Debug Config")


If you have the actual badge board you'll need debugging to look like this:


![alt text](https://github.com/thedarknet/defcon25-badge/blob/master/Debug-ScreenShot-OnBadge.png "Debug Config")
