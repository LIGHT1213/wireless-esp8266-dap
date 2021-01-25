<p align="center"><img src="https://user-images.githubusercontent.com/17078589/73821108-300bda00-482e-11ea-89f6-011a50037a12.png"/></p>
<h1 align="center">Wireless ESP8266 DAP</h1>

[![Build Status](https://github.com/windowsair/wireless-esp8266-dap/workflows/build/badge.svg?branch=master)](https://github.com/windowsair/wireless-esp8266-dap/actions?query=branch%3Amaster) master
[![Build Status](https://github.com/windowsair/wireless-esp8266-dap/workflows/build/badge.svg?branch=develop)](https://github.com/windowsair/wireless-esp8266-dap/actions?query=branch%3Adevelop) develop

[![](https://img.shields.io/badge/license-MIT-green.svg?style=flat-square)](https://github.com/windowsair/wireless-esp8266-dap/LICENSE) [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-blue.svg?style=flat-square)](https://github.com/windowsair/wireless-esp8266-dap/pulls) [![%e2%9d%a4](https://img.shields.io/badge/made%20with-%e2%9d%a4-ff69b4.svg?style=flat-square)](https://github.com/windowsair/wireless-esp8266-dap)


## Introduce

Wireless debugging with ***only one ESP8266*** !

Realized by USBIP and CMSIS-DAP protocol stack.

> 👉 5m distance, 100kb size firmware flash test:

<p align="center"><img src="https://user-images.githubusercontent.com/17078589/73829782-808b3380-483e-11ea-8389-1570bc4200af.gif"/></p>

## Feature

1. Debug Communication Mode
    - [x] SWD
    - [x] JTAG

2. USB Communication Mode
    - [x] USB-HID (Default)
    - [x] WCID & WinUSB (Experimental)

3. Debug Trace
    - [ ] UART Serial Wire Output(SWO)
    - [ ] SWO Streaming Trace

4. More..
    - [x] Custom maximum debug clock (40MHz, SWD only)
    - [x] SWD protocol based on SPI acceleration
    - [x] ...



## Link your board

### WIFI

The default connected WIFI SSID is `DAP` , password `12345678`

You can change `WIFI_SSID` and ` WIFI_PASS` in [wifi_configuration.h](main/wifi_configuration.h)

### Debugger




| SWD            |        |
|----------------|--------|
| SWCLK          | GPIO5  |
| SWDIO          | GPIO4  |
| LED\_CONNECTED | GPIO2  |
| LED\_RUNNING   | GPIO15 |
| TVCC           | 3V3    |
| GND            | GND    |


--------------


| JTAG               |         |
|--------------------|---------|
| TCK                | GPIO5   |
| TMS                | GPIO4   |
| TDI                | GPIO13  |
| TDO                | GPIO12  |
| nTRST \(optional\) | GPIO0\* |
| nRESET             | GPIO14  |
| LED\_CONNECTED     | GPIO2   |
| LED\_RUNNING       | GPIO15  |
| TVCC               | 3V3     |
| GND                | GND     |

You can modify these pin definitions in [DAP_config.h](components/DAP/config/DAP_config.h)

> Tips: Try to avoid using `GPIO0`(working mode switch) and `GPIO16`(RTC)

## Build

You can build locally or use Github Action to build online

### Build with Github Action Online

See: [Build with Github Action](https://github.com/windowsair/wireless-esp8266-dap/wiki/Build-with-Github-Action)

### General build

1. Get ESP8266 RTOS Software Development Kit

    For now, use the 3.3-rc1 version of the SDK (this is a known issue)
    See: [ESP8266_RTOS_SDK](https://github.com/espressif/ESP8266_RTOS_SDK/releases/tag/v3.3-rc1 "ESP8266_RTOS_SDK")

2. Build & Flash

    Build with ESP-IDF build system.
    More information can be found at the following link: [Build System](https://docs.espressif.com/projects/esp-idf/en/latest/api-guides/build-system.html "Build System")

The following example shows a possible way to build on Windows:

```bash
# Build
python ./idf.py build
# Flash
python ./idf.py -p /dev/ttyS5 flash
```

> We also provided sample firmware quick evaluation. See [Releases](https://github.com/windowsair/wireless-esp8266-dap/releases)


## Usage

1. Get USBIP project

- Windows: [usbip-win](https://github.com/cezanne/usbip-win) . 
>  The pre-compiled version on SourceForge is also available, for HID mode only, but it may be faster.
- Linux: Distributed as part of the kernel

2. Start esp8266 and connect it to the device to be debugged

3. Connect it with usbip:

```bash
# HID Mode only
# for pre-compiled version on SourceForge
# or usbip old version
.\usbip.exe -D -a <your-esp8266-ip-address>  1-1

# HID Mode Or WinUSB Mode
# for usbip-win 0.3.0 kmdf ude
.\usbip.exe attach_ude -r <your-esp8266-ip-address> -b 1-1

```

If all goes well, you should see your device connected.

![image](https://user-images.githubusercontent.com/17078589/73833411-eb3f6d80-4844-11ea-8501-02a008f6119d.png)


Then test it under MDK:

![target](https://user-images.githubusercontent.com/17078589/73830040-eb3c6f00-483e-11ea-85ee-c40b68a836b2.png)



## Develop

0.  Check other branches to know the latest development progress.

1. Use WinUSB Mode: 

    change `USE_WINUSB` macor in [USBd_config.h](components/USBIP/USBd_config.h)



> Credits to:
> - https://github.com/thevoidnn/esp8266-wifi-cmsis-dap for adapter firmware based on CMSIS-DAP v1.0
> - https://github.com/ARM-software/CMSIS_5 for CMSIS
> - https://github.com/cezanne/usbip-win for usbip windows


In this repo you can find the complete implementation of the USB protocol stack including USB-HID, WCID, WinUSB. ~~Although WinUSB-based mode currently does not work on USBIP~~ :disappointed_relieved: . They are very easy and can help you quickly build your own DAP on other hardware platforms.


Currently TCP transmission speed needs to be further improved, If you have any ideas, welcome:
- [New issues](https://github.com/windowsair/wireless-esp8266-dap/issues)
- [New pull](https://github.com/windowsair/wireless-esp8266-dap/pulls)


### Issue

2020.12.1

TCP transmission speed needs to be further improved.

2020.11.11

Winusb is now available, but it is very slow.


2020.2.4

Due to the limitation of USB-HID (I'm not sure if this is a problem with USBIP or Windows), now each URB packet can only reach 255 bytes (About 1MBps bandwidth), which has not reached the upper limit of ESP8266 transmission bandwidth.

I now have an idea to construct a Man-in-the-middle between the two to forward traffic, thereby increasing the bandwidth of each transmission.

2020.1.31

At present, the adaptation to WCID, WinUSB, etc. has all been completed. However, when transmitting data on the endpoint, we received an error message from USBIP. This is most likely a problem with the USBIP project itself.

Due to the completeness of the USBIP protocol document, we have not yet understood its role in the Bulk transmission process, which may also lead to errors in subsequent processes.

We will continue to try to make it work on USB HID. Once the USBIP problem is solved, we will immediately transfer it to work on WinUSB



## License
[MIT LICENSE](LICENSE)