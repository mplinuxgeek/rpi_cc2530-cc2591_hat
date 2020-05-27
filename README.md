# CC2531+CC2591 hat

THis repo provides documentation and design files (when they are ocmplate) for building a CC2531+CC2591 hat for Raspberry Pi, the Hat provides both flashing and serial communication, so there is no need for a CC Debug or a USB to Serial dongle.

![Alt text](images/pi_cc2531.jpg?raw=true "Title")

# Raspberry Pi UART

Add enable_uart=1 to /boot/config.txt, reboot required to apply change.

On devices with built in bluetooth (Zero, 3, 4) the bluetooth module uses /dev/serial1 which is a link that points to /dev/ttyAMA0.

```pi@raspberrypi:~ $ ls -lah /dev/serial*
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
