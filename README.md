Arduino library for Futaba SBUS
===============================

This library allows your arduino to read Futaba SBUS messages, and decode the servo positions & flags in it.

I used the following sources of info to write it:
* https://mbed.org/users/Digixx/notebook/futaba-s-bus-controlled-by-mbed/

To connect this to your Arduino, you'll need an inverter cable such as http://www.hobbyking.com/hobbyking/store/__24523__ZYX_S_S_BUS_Connection_Cable.html, or you can build your own using a TTL inverter or the schematic in the included SBUS_inverter.jpeg file.

![correct inverter](https://github.com/zendes/SBUS/blob/master/SBUS_inverter.jpeg)

Basic Installation
------------------
```
$ cd <arduino-sketchbook>/libraries
$ git clone https://github.com/zendes/SBUS.git
```

* `<arduino-sketchbook>` is `~/Documents/Arduino/libraries` on OSX

Then restart your Arduino IDE and take a look at the examples.


S-BUS protocol
==============

The protocol is 25 Byte long and is send every 14ms (analog mode) or 7ms (highspeed mode).
One Byte = 1 startbit + 8 databit + 1 paritybit + 2 stopbit (8E2), baudrate = 100'000 bit/s
The highest bit is send first. The logic is inverted (Level High = 1)

```
[startbyte] [data1] [data2] .... [data22] [flags] [endbyte]

startbyte = 11110000b (0xF0)
endbyte   = 00000000b (0x00)
```

Data format
---------------

data 1-22 = `[ch1, 11bit][ch2, 11bit] .... [ch16, 11bit]` (ch# = 0 bis 2047)

* channel 1 uses 8 bits from data1 and 3 bits from data2
* channel 2 uses last 5 bits from data2 and 6 bits from data3
* etc.


Flags
-----

The flag byte is as follows

```
0 0 0 0   0 0 0 0
| | | |
| | | \-------------- failsafe activated (0x10)
| | \---------------- Frame lost, equivalent red LED on receiver (0x20)
| \------------------ ch18 = digital channel (0x40)
\-------------------- ch17 = digital channel (0x80)
```


