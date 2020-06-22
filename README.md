# CC2530+CC2591 hat

This repo provides documentation and design files (when they are complate) for building a CC2530+CC2591 hat for Raspberry Pi.

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

![Alt text](images/Zigbeegw.jpg?raw=true "Title")

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



```
git clone https://github.com/jmichault/flash_cc2531.git
./cc_chipid
./cc_read firmware_$(date +"%m_%d_%Y").hex
./cc_erase
./cc_write CC2530ZNP-Prod.hex
```



DS18B20

https://github.com/adafruit/Adafruit_Learning_System_Guides/blob/master/Raspberry_Pi_DS18B20_Temperature_Sensing/thermometer.py



I2C
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

SPI
Add the below to /boot/config.txt
```
dtparam=spi=on
```



