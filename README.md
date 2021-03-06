# ifs485hat
The bootstrap code for setting up environment for our dedicated hardware rpi 485 hat. 
https://store.invisiblefun.com/collections/frontpage/products/ifs-485-hat-with-rtc

![alt text](https://cdn.shopify.com/s/files/1/0261/8549/8696/products/IMG_3062_1024x1024@2x.png?v=1606290624)


## Introduction : 
The IFS RPI 485 HAT will enable your Raspberry Pi to communicate with other devices stably in long-distance via RS485 functions. Also, you can access real time by IFS RPI 485 HAT. The real time clock maintains year, month, date, hours, minutes, seconds.


## Product Description: 
The IFS RPI 485 HAT is a UART to RS485 serial converter for the Raspberry Pi. The RS485 Port is connected to the UART port on the Raspberry Pi using a MAX13487 interface. The MAX13487 IC converts the 5V UART port to RS485 and allow communication with compatible devices. 

Our IFS RPI 485 HAT contains protection using TVS Diodes in SMBJ series, which is designed specifically to protect sensitive electronic equipment from voltage transients by lightning and other transient voltage events.

For the real time clock, it is done by an extremely accurate IC, DS3231MZ. The device incorporates a battery input to maintain accurate timekeeping when the main power to the device is interrupted. 


## Specifications:
Dimension : 64.87mm x 56.33mm <br/>
Mounting hole size: 3.0mm


## Remark: 
The IFS RPI 485 HAT can also be used without Raspberry Pi. There are pinouts named RX, TX, 5V and GND, which allow you to use it for RS485 communication only.
The battery of real time clock involves CR2032 in 3V to run.


## User Guide 
**Go to Raspberry Pi Software Configuration Tool to enble i2C and Uart interface**
```
sudo raspi-config
```

It has the following menu: 
```
┌───────────────────┤ Raspberry Pi Software Configuration Tool (raspi-config) ├────────────────────┐ 
│                                                                                                  │ 
│        1 System Options       Configure system settings                                          │ 
│        2 Display Options      Configure display settings                                         │ 
│        3 Interface Options    Configure connections to peripherals                               │ 
│        4 Performance Options  Configure performance settings                                     │ 
│        5 Localisation Options Configure language and regional settings                           │ 
│        6 Advanced Options     Configure advanced settings                                        │           
│        8 Update               Update this tool to the latest version                             │ 
│        9 About raspi-config   Information about this configuration tool                          │ 
│                                                                                                  │ 
│                                                                                                  │ 
│                           <Select>                           <Finish>                            │ 
│                                                                                                  │ 
└──────────────────────────────────────────────────────────────────────────────────────────────────┘ 
```

Goes to ```3 Interfacae Option``` and press ```enter``` key.

It has the following menu: 
```
┌───────────────────┤ Raspberry Pi Software Configuration Tool (raspi-config) ├────────────────────┐ 
│                                                                                                  │ 
│        P1 Camera       Enable/disable connection to the Raspberry Pi Camera                      |
|        P2 SSH         Enable/Disable remote command line access to your Pi using SSH             │ 
│        P3 VNC         Enable/Disable graphical remote access to your Pi using RealVNC            │ 
│        P4 SPI         Enable/Disable automatic loading of SPI kernel module                      │ 
│        P5 I2C         Enable/Disable automatic loading of I2C kernel module                      │ 
│        P6 Serial      Enable/Disable shell and kernel messages on the serial connection          │ 
│        P7 1-Wire      Enable/Disable one-wire interface                                          │ 
│        P8 Remote GPIO Enable/Disable remote access to GPIO pins                                  │ 
│                                                                                                  │ 
│                                                                                                  │ 
│                           <Select>                           <Finish>                            │ 
│                                                                                                  │ 
└──────────────────────────────────────────────────────────────────────────────────────────────────┘ 
```

Goes to ``` P5 I2C Enable/Disable automatic loading of I2C kernel module``` and press ```Enter``` key. <br/>
select ```yes``` and ```ok```

## 
**Go to Raspberry Pi Software Configuration Tool Again**
```
sudo raspi-config
```

Goes to ```3 Interfacae Option``` and press ```enter``` key. <br/>
Goes to ``` P6 Serial Enable/Disable shell and kernel messages on the serial connection``` and press ```Enter``` key. <br/>
select ```no``` and then ```yes``` and then ```ok```.
** Do not enable access of shell over uart!!!

**Leave Raspberry Pi Software Configuration Tool**

click ```Tab``` and ```Tab``` and ```Enter``` <br/>


**Raspbian Jessie (Systemd)**

You can add support for the RTC by adding a device tree overlay. Run ```sudo nano /boot/config.txt``` to edit the pi configuration and add whichever matches your RTC chip: ```dtoverlay=i2c-rtc,ds3231``` to the end of the file.

Save it and run ```sudo reboot``` to start again. Log in and run ```sudo i2cdetect -y 1``` to see the UU show up where 0x68 should be

```    
    0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- -- --
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- UU -- -- -- -- -- -- --
70: -- -- -- -- -- -- -- --
```

Disable the "fake hwclock" which interferes with the 'real' hwclock <br/>
```sudo apt-get -y remove fake-hwclock``` <br/> 
OR <br/>
```sudo update-rc.d -f fake-hwclock remove``` <br/>
OR <br/>
```sudo systemctl disable fake-hwclock``` <br/>

Now with the fake-hw clock off, you can start the original 'hardware clock' script.

Run ```sudo nano /lib/udev/hwclock-set``` and comment out these three lines:

```
#if [ -e /run/systemd/system ] ; then
#exit 0
#fi
```

Also comment out the two lines
```/sbin/hwclock --rtc=$dev --systz --badyear```
and 
```/sbin/hwclock --rtc=$dev --systz```


**Sync time from Pi to RTC**

When you first plug in the RTC module, it's going to have the wrong time because it has to be set once. You can always read the time directly from the RTC with ```sudo hwclock -D -r```

You can see, the date at first is invalid! You can set the correct time easily. First run ```date``` to verify the time is correct. Plug in Ethernet or WiFi to let the Pi sync the right time from the Internet. Once that's done, run ```sudo hwclock -w``` to write the time, and another ```sudo hwclock -r``` to read the time


Once the time is set, make sure the coin cell battery is inserted so that the time is saved. You only have to set the time once

That's it! Next time you boot the time will automatically be synced from the RTC module
