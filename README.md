# Introduction

This is a skeleton repository for embedded ARM development under
Linux. It is intended to be used within a Vagrant VM that is available
at <provide link here>

If you already have the `gcc-arm-embedded` environment installed
on your Linux machine then simply cloning this repository will
get you started.

The example code in the `src` directory is a minimal example
that combines "blinky" operation and a USART driver that toggles
the LED every time a new character is sent our the serial port.

The example is linked for an stm32nucleo F401RE board and exercises
the `stlink` debug interface and the USB CDC-ACM serial interface
at the same time.

# Usage

If you don't already have a Linux machine set up for ARM development
the very easiest thing to do is to set up a Vagrant box using the 
repository <here> as a starting point.

Start up the Vagrant box and it will provision itself with the 
following additional packages:

- Adds `ppa:team-gcc-arm-embedded/ppa` to the list of Ubuntu package hosts
- Does an `apt-get-update`
- Installs `gcc-arm-embedded`
- Installs `openocd`
- Installs `minicom`
- Clones the `armdev-skeleton` repository
- Builds the `libopencm3` libraries

Once that's done, you need to go into `armdev-skeleton/libopencm3` and
run `make` - the result is that there should be a `usart.elf` file in 
your directory.

Assuming that you have an stm32nucleo F401RE board, plug it into your
development machine.

The setup of the Vagrant box should have set up a capture on the USB
port for the STLink device and if you run `dmesg` on the VM you should
see something like:

```
i[ 9876.410962] usb 2-1: new full-speed USB device number 10 using ohci-pci
[ 9876.707138] usb 2-1: New USB device found, idVendor=0483, idProduct=374b
[ 9876.707145] usb 2-1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[ 9876.707148] usb 2-1: Product: STM32 STLink
[ 9876.707151] usb 2-1: Manufacturer: STMicroelectronics
[ 9876.707154] usb 2-1: SerialNumber: 0672FF575056805087053719
[ 9876.791744] usb-storage 2-1:1.1: USB Mass Storage device detected
[ 9876.792015] scsi host7: usb-storage 2-1:1.1
[ 9876.796580] cdc_acm 2-1:1.2: ttyACM0: USB ACM device
[ 9877.809063] scsi 7:0:0:0: Direct-Access     MBED     microcontroller  1.0  PQ: 0 ANSI: 2
[ 9877.811742] sd 7:0:0:0: Attached scsi generic sg1 type 0
[ 9877.827873] sd 7:0:0:0: [sdb] 1072 512-byte logical blocks: (549 kB/536 KiB)
[ 9877.838809] sd 7:0:0:0: [sdb] Write Protect is off
[ 9877.838818] sd 7:0:0:0: [sdb] Mode Sense: 03 00 00 00
[ 9877.849703] sd 7:0:0:0: [sdb] No Caching mode page found
[ 9877.849749] sd 7:0:0:0: [sdb] Assuming drive cache: write through
[ 9877.935509]  sdb:
[ 9877.997375] sd 7:0:0:0: [sdb] Attached SCSI removable disk
```

# Start Debugging

Now we're ready to start debugging! Type `scripts/debug usart` and the
debugger should come up. Now type `load` and the file should be loaded 
into the target FLASH and it's ready to run with `continue`.

The LED on the board should be toggling - so now connect the `minicom`
terminal - most likely from another `ssh` session to the VM using 
`minicom -D /dev/ttyACM0`. Of course if your serial port is not at `ttyACM0`
based on the `dmesg` output, fill in the correct value.
