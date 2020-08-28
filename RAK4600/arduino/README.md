# Change log
2020.4.10

1. Support shtc3,lps22hb,bme689
2. Support GPIO

# RAK4600 LPWAN Evaluation Board Quick Start Guide
2020.1.4

![rak4600_eb](/RAK4600/image/rak4600_eb.jpg)

## Content
- [Introduction](#introduction)    
- [Safety information](#safety-information)    
- [Hardware Setup](#hardware-setup)
- [AT Commands Tool](#at-commands-tool)
- [Arduino IDE BSP Installation](#arduino-ide-bsp-installation)
- [PlatformIO BSP Installation](#platformio-bsp-installation)

## Introduction

RAK4600 LPWAN Evaluation Board is a WisBlock product that consists of RAK4600 LPWAN Module and RAK5005 WisBase.
This development board allows access to all serial and GPIO ports for easy configuration. The RAK4600 core module includes nRF52832 MCU and SX1276 LoRa® chip to help you quickly and easily develop/test your firmware. 
The board complies with LoRaWAN® 1.0.2 protocols and also supports LoRa® Point to Point communications.
Product features:

1. Ultra-Low Power Consumption 2.0uA in sleep mode
2. LoRa Boost mode with 20dBm output power
3. BLE 5.0 (Tx power -20 to +4 dBm in 4dB steps)

Its RF communication capabilities (LoRa+BLE) make it suitable for a variety of applications in the IoT field. More details can be found in this link：

[RAK4600 Evaluation Board ](https://doc.rakwireless.com/rak4600-lora-evaluation-board/overview)

RAK4600 LPWAN Evaluation Board can be ordered in RAK store:

https://store.rakwireless.com/products/rak4600-evaluation-board

### Safety Information

>Before powering the RAK4600 LPWAN Evaluation Board , you should install the LoRa® and BLE Antenna first. Not doing so might damage the board.

![antenna](/RAK4600/image/antenna.png)

>Please don't unplug any hardware connector when you are uploading code into it, otherwise Evaluation Board may become unresponsive.

## Hardware Setup
What do you need?
Before going through the Hardware Setup guide of the RAK4600 LPWAN Evaluation Board, make sure to prepare the items listed below:

1. RAK4600 LPWAN Evaluation Board 
2. Micro USB Cable
3. Windows PC

Connect your RAK4600 LPWAN Evaluation Board to your Windows Machine using the provided Micro USB cable.

![rakpc](/RAK4600/image/rakpc.png)

### Device Firmware Setup

**Note**：
This device released by RAKWireless is already pre-loaded with its latest BootLoader firmware upon manufacture.
Normally, you don't need to reflash the BootLoader and you can jump to Section [Arduino IDE BSP Installation](#arduino-ide-bsp-installation).
If you want to have your device's firmware burned or upgraded, please refer to the link below:

https://doc.rakwireless.com/rak4600-lora-evaluation-board/burning-the-firmware-into-the-device

You can also refer to the following link to customize your own bootloader:[Adafruit_nRF52_Bootloader](https://github.com/adafruit/Adafruit_nRF52_Bootloader) 

![DFU](/RAK4600/image/DFU%20Log.png)

### Device Firmware Upgrade

The bootloader of RAK4600 supports the serial port upgrade program. Use the Arduino IDE to upgrade as follows.

1. Connect the pin 18 of MCU to the GND.
2. After restarting the MCU, disconnect pin 18 from GND. The current state is bootloader mode.
3. Click the `Upload` button to update the firmware to RAK4600.

**Note**：

​>   1. On RAK4600 Evaluation Board the silk screen of pin 18 on the back of the board is "**RX1**".

![DFU](/RAK4600/image/rak4600_back.jpg)

## AT Commands Tool

<p>If you want to test the LoRaWan functions on RAK4600 Evaluation Board you can use RAK Serial Port Tool: https://downloads.rakwireless.com/LoRa/Tools/RAK_SERIAL_PORT_TOOL_V1.2.1.zip<br>
In order to choose the correct COM Port number for your device you need to go to your Device Manager by pressing : Windows + R and typing devmgmt.msc or search in the Start Menu
</p>

![devmanager](/RAK4600/image/dev_manager.png)

Open RAK Serial Port Tool, choose the Port Number from the device manager and the Correct Baud Rate then click Open:

![at_serial](/RAK4600/image/at_serial_tool.png)

The link below shows the AT Commands available for use:
https://doc.rakwireless.com/rak4600-lora-evaluation-board/at-commands-for-rak4600

## Arduino IDE BSP Installation

The following describes how the board builds the development environment in Arduino IDE and runs BLE and LoRa application examples.

1. What is Arduino?
     If you know little about Arduino, please have a look below: 
     [https://www.arduino.cc/](https://www.arduino.cc/) 

2. Download and install the Arduino IDE first: 
     [https://www.arduino.cc/en/Main/Software](https://www.arduino.cc/en/Main/Software)
	 
>Note: Install at least v1.6.12 Arduino IDE version.
 
3. Start the Arduino IDE 

4. Go to File->Preferences Tab

Add 'https://downloads.rakwireless.com/Arduino/BSP/package_raknrf_index.json' as a new line in the 'Additional Board Manager URL' list.(See images below)

![file_preferences](/RAK4600/image/file_preferences.png)

![board_manager](/RAK4600/image/board_manager.png)


5. Restart IDE and Open Board Manager tool : Tools -> Board -> Board Manager

On Board Manager type RAK then click install 'RAK Wireless nRF Boards'.

![rak_boards](/RAK4600/image/rak_boards.png)

6. Once the BSP is installed, select 'WisBlock Core RAK4601 Board' from the Tools -> Board menu, which will update your system config to use the right compiler and settings for the board.

![rak_arduino](/RAK4600/image/rak_arduino.png)

### BLE example

1. Open the BLE_UART demo : File->Examples->RAK WisBlock Example->RAK4601->BLE_UART

![ble_uart](/RAK4600/image/ble_uart.png)

2. Compile the project and upload to RAK4600 board. View the log through the serial port on the PC.

3. We need to install an app in the mobile phone to connect with the Evaluation Board.

   Refer to the following link for installation. [nRF Connect for Mobile - nordicsemi.com](https://www.nordicsemi.com/Software-and-tools/Development-Tools/nRF-Connect-for-mobile)  

4. After the installation is completed, enable Bluetooth and open app. Scan and connect a device named "bluefruit 52". You can communicate with your device via Bluetooth.

![ble_scan](/RAK4600/image/ble_scan.png) 

6. Here are the logs of mobile phone and PC。

![ble_mobile_app](/RAK4600/image/ble_mobile_app.jpg) 

![ble_pc_tool](/RAK4600/image/ble_pc_tool.png) 


### LoRa example

RAK4600 uses open source protocol stack to realize LoRaWAN communication.

1. What LoraWAN lib is used?

LMIC is a LoraWAN-MAC-in-C library, adapted to run under the Arduino environment.

Refer to the following link to install it.

[https://github.com/mcci-catena/arduino-lmic](https://github.com/mcci-catena/arduino-lmic) 

Open Tools->Manage Library and install MCCI LoRaWan LMIC Library

![mcci_library](/RAK4600/image/mcci_library.png) 

2. Open the example project file `LoRaWAN_Demo.ino` in Arduino IDE: File->Examples->RAK WisBlock Example->LoRaWAN_Demo

Modify the device parameters to your own.

>Notice:The APPEUI and DEVEUI must be in **little-endian format**.

![Device Parameter](/RAK4600/image/LoRa_OTAA_Parameters.png)

>Please note that the default region for MCCI LoRaWAN LMIC Library is US915, if you want to use another region like EU868, you should open the file "lmic_project_config.h" in the folder <...\Documents\Arduino\libraries\arduino-lmic-master\project_config>, and modify the region to the one you want to use.

![Region](/RAK4600/image/Arduino-LoRa%20region%20configuration.png)

3. Compile the project and upload to RAK4600 board. When the upload is finished, the RAK4600 will be auto join LoRaWAN and send data.

![Run information](/RAK4600/image/running%20information.png) 

### BLE + LoRa example

Open the example project file `BLE_Scan_LoRa.ino` in Arduino IDE: File->Examples->RAK WisBlock Example->BLE_Scan_LoRa

Modify the LoRa parameters to your own, compile and upload it as before.

We can see the following logs.

![Run information](/RAK4600/image/ble%2Blora.png)

## PlatformIO BSP Installation

### Install PlatformIO

First of all, install VS code which is a great and open source tool, and you can download it here:

https://code.visualstudio.com/

After installing VS code, you can search PlatformIO extension and install it:

![Ext Platformio](/RAK4600/image/ext_platformio.png)


### Install Nordic nRF52 Arduino framework 

After installing PlatformIO, you can see the PlatformIO icon and open it as follow:

![PlatformIO](/RAK4600/image/platformio.png)

Open "Platforms" in PlatformIO and search "Nordic" as follow:

![Nordic Platform](/RAK4600/image/nordic_platform.png)

You can see there are several items, just click "Nordic nRF52" item and "Install" it as follow:

![board_nordic](/RAK4600/image/board_nordic.png)

![nrf52_platform](/RAK4600/image/nrf52_platform.png)

### Get patch files for RAK WisBlock Core boards 

Download the PlatformIO folder from:

https://github.com/RAKWireless/WisBlock/tree/master/PlatformIO/

and save it on your computer.

### Add WisBlock Core RAK4631 to the platform

Copy the file **`wiscore_rak4631.json`** to the boards folder of the nordic nrf52 platform folder.

The path maybe like: 

 - Windows: %USER%\.platformio\platforms\nordicnrf52\boards

Then, create a new project in PlatformIO:

![add_project](/RAK4600/image/add_project.png)

Choose "WisCore RAK4631 Board (RAKwireless)" for "Board" item, and choose "Arduino" for "Framework" item as follow:

![project_wizard](/RAK4600/image/project_wizard.png)

After creating successfully, you can see the project:

![wisblock](/RAK4600/image/wisblock.png)

### Add WisBlock Core RAK4631 to the package

Now, Copy the complete folder "WisCore_RAK4631_Board" in the "PlatformIO" folder which you just downloaded to the framework-arduinoadafruitnrf52 package variants folder. The path maybe like:
 - Windows: %USER%\.platformio\packages\framework-arduinoadafruitnrf52\variants

Finally, restart the PlatformIO.

OK, you've install and configure PlatformIO for WisBlock successfully.

### First example code

Now, let's try to compile an example of WisBlock using PlatformIO. We use the LoRaWAN® OTAA example in this document:

https://github.com/RAKWireless/WisBlock/tree/master/examples/communications/LoRa/LoRaWAN/LoRaWAN_OTAA/

Just copy the source code of the .ino file into the main.cpp of the PlatformIO project we just created:

![lorawan_vscode](/RAK4600/image/lorawan_vscode.png)

Then we need to install the LoRaWAN® library "SX126x-Arduino" in PlatformIO firstly because this example is built based on this library. Just search "SX126x" in "Libraries" item of PlatformIO, and you can see "SX126x-Arduino" as follow:

![radiolib](/RAK4600/image/radiolib.png)

Just click it and "Install" this library as follow:

![sx126x](/RAK4600/image/sx126x.png)

Then compile it by click the compiling icon at the bottom tool bar as follow:

![](/RAK4600/image/.png) 

![compiled_vscode](/RAK4600/image/compiled_vscode.png)

Great! We've compiled this example successfully!

Same as in Arduino IDE, there is an upload icon (red circle) on the right of the compiling icon (green circle) which can be used to upload the compiled firmware into your device.

![pio-flash](/RAK4600/image/pio-flash.jpg)


## Credits

The Arduino IDE BSP is based on Adafruits nRF52 BSP,
which is based on Arduino-nRF5 by Sandeep Mistry,
which in turn is based on the Arduino SAMD Core.

## Support peripheral

LoraWAN, BLE, lis3dh, opt3001
