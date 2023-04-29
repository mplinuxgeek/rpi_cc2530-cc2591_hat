# CC2530+CC2591 hat

![Alt text](images/Zigbeegw.jpg?raw=true "Title")

This repo provides documentation and design files (when they are complete) for building a CC2530+CC2591 hat for Raspberry Pi.

Features:
- Serial comunnication with the CC module
- Data lines for flashing firmware
- Onboard 3.3v 500mA low drop out regulator
- Jumper for selecting between the onboard regulator or Raspberry Pi's regulator
- Power LED
- RX and TX LEDs
- DS18B20 footprint for onboard sensor and header for external sensors with pullup resistor
- 2x I2C headers
- 1x SPI header

Component BOM:

Part|Name|Qty
------------- | ------------- | -------------
U1|MIC5216 3.3V 500mA LDO Regulator|1
C1|10 µF 0603 Capacitor|1
C3|1 µF 0603 Tantalum Capacitor|1
3V3|0603 LED Red|1
TX|0603 LED Green|1
RX|0603 LED Orage|1
R1,R4|4K7 0603 Resistor|2
R2,R3|56 0603 Resistor|2
U2|Optional DS18B20 on board|1
P1*|2x6 2.54mm male header|1
P2*|2x7 2.54mm male header|1
RPI|2x20 2.54mm female header|1
^|1x20 2.54mm male header|1
J1|2 pin jumper|1

\* P1 and P2 are not marked on the board but only fit in one place.

^ 1x20 pin header strip to be cut up and used in various sockets on the board.

J1 (labled as "Pi - Reg") is used to select between the Raspberry Pi's 3.3v rail or the onboard regulator, this was used for prototyping and could be bypassed by jumping a piece of wire between center pad and Reg pad.


# Raspberry Pi UART

Add enable_uart=1 to /boot/config.txt, reboot required to apply change.

On devices with built in bluetooth (Zero, 3, 4) the bluetooth module uses /dev/serial1 which is a link that points to /dev/ttyAMA0.

```
pi@raspberrypi:~ $ ls -lah /dev/serial*
lrwxrwxrwx 1 root root 5 May 27 14:56 /dev/serial0 -> ttyS0
lrwxrwxrwx 1 root root 7 May 27 14:56 /dev/serial1 -> ttyAMA0
```

Model|Serial|Tested
------------- | ------------- | -------------
Zero|/dev/ttyAMA0|
Zero W|/dev/ttyS0|
1|/dev/ttyAMA0|
2|/dev/ttyAMA0|Yes
3|/dev/ttyS0|Yes
4|/dev/ttyS0|

You may need to disable the serial console

# Disable Linux serial console
https://www.raspberrypi.org/documentation/configuration/uart.md

By default, the primary UART is assigned to the Linux console. If you wish to use the primary UART for other purposes, you must reconfigure Raspberry Pi OS. This can be done by using raspi-config:

Start raspi-config: `sudo raspi-config`.

Select option 5 - interfacing options.

Select option P6 - serial.

At the prompt Would you like a login shell to be accessible over serial? answer 'No'

At the prompt Would you like the serial port hardware to be enabled? answer 'Yes'

Exit raspi-config and reboot the Pi for changes to take effect.

# Flashing without a hat

If you don't have a hat you can connect a CC2530 to a Raspberry Pi using the below pins.

CC2530|Raspberry Pi
------------- | -------------
GND|Pin 39 (GND)
VCC|Pin 01 (3.3V)
RES or RST or R (reset)|Pin 35 (GPIO24, BCM19)
P22 (DC)|Pin 36 (GPIO27, BCM16)
P21 (DD)|Pin 38 (GPIO28, BCM20)

# Install flash_cc2531 and flash firmware
```
sudo apt-get install wiringpi

git clone https://github.com/jmichault/flash_cc2531.git
```

The flash_cc2531 repo contains pre-compiled binaries for Raspberry Pi so there is no need to compile the source.

We can jump straight to testing:
```
cd flash_cc2531

./cc_chipid 
```
The chipid command should return something like:
```
  ID = a524.
```

To confirm its working as expected we can dump the current firmware:
```
$ ./cc_read firmware_$(date +"%m_%d_%Y").hex
```

The result should liook like this:
```
  ID = a524.
 reading 256k/256k
```

Now we can flash the ZStack firmware, first step is to erase the flash on the CC module:
```
./cc_erase
```

Now obtain a firmware image from Koenkk's github repo, https://github.com/Koenkk/Z-Stack-firmware/tree/master/coordinator

For my particular CC modules I used: https://raw.githubusercontent.com/Koenkk/Z-Stack-firmware/master/coordinator/Z-Stack_Home_1.2/bin/default/CC2530_CC2591_DEFAULT_20190608.zip

```
./cc_write CC2530ZNP-Prod.hex
```

If all went well the output should look like this:
```
  ID = a524.
  reading line 15490.
  file loaded (15497 lines read).
writing page 128/128.
verifying page 128/128.
 flash OK.
```

If the process is stuck at "writing page 1/128" then you may have skipped the erase step, repeat the erase before trying the write again.

The CC module is now ready to be used and Zigbee2mqtt can now be installed.


# DS18B20

https://github.com/adafruit/Adafruit_Learning_System_Guides/blob/master/Raspberry_Pi_DS18B20_Temperature_Sensing/thermometer.py



# I2C
Add the below to /boot/config.txt
```
dtparam=i2c1=on
```

Reboot Pi
Run the below command:
```
lsmod | grep i2c
```

if i2c-dev is not listed run the below command, then add i2c-dev to /etc/modules:
```
sudo modprobe i2c-dev
```
Confirm I2C devices are detected with the below command:
```
$ sudo i2cdetect -y 1
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- -- -- 
10: -- -- -- -- -- -- -- -- 18 -- -- -- -- -- -- -- 
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
70: -- -- -- -- -- -- -- --                      
```

# SPI
Add the below to /boot/config.txt
```
dtparam=spi=on
```



