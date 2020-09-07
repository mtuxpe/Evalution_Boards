# RAK4600 LoRaWan® Demo example

## 1. Overview

This example shows how to setup RAK4600 LPWAN Evaluation Board as a LoRaWan® sensor node.
It sends a valid LoRaWAN packet with payload "Hello_%d", using frequency and encryption settings matching those of the The Things Network. 
The parameter %d is the packet counter.If you want to use LoRaWan® communication protocol you need to define a region. 
The defined region tells the library which frequency and which channels should be used.
Valid regions for MCCI LoRaWan LMIC library:  

eu_868: EU
us_915: US
au_921: Australia 
as_923: Asia
in_866: India

To use this sketch, first register your application and device with the things network, to set or generate an AppEUI, DevEUI and AppKey.
Multiple devices can use the same AppEUI, but each device has its own DevEUI and AppKey.

OTAA
- Device EUI if you want to use ABP registration of the device
- Application EUI
- Application Key, the AES encryption/decryption cipher application key

The devices must be registered on your LoRaWan® server before they can send and receive data. 
The above mentioned EUIs and keys can be obtained from your LoRaWan® server. 
If you use a RAK gateway with an integrated LoRaWan® server or want to use TheThingsNetwork server you can find a tutorial [here](https://doc.rakwireless.com/rak7258-micro-gateway/overview)     

## 2. Hardware Required

- RAK4600 Evaluation Board
- Valid LoRa gateway

## 3. Software Required

### 3.1 Installation of MCCI LoRaWAN LMIC library

After install Arduino IDE and BSP according to the Quick Start Guide, you can install LoRaWAN® library now. Step as below:

- Start the Arduino IDE
- Open Library Manager: Tools->Manage Library and search for MCCI LoRaWan LMIC
- Install MCCI LoRaWan LMIC if not installed.

![mcci_library](/RAK4600/image/mcci_library.png)

>Note: This library requires Arduino IDE version 1.6.6 or above, since it requires C99 mode to be enabled by default.

### 3.2 Features of MCCI LoRaWAN LMIC library

The MCCI LoRaWAN library takes care of all logical MAC states and timing constraints and drives the Semtech SX1276 LoRa radio.
Only a limited number of features was tested on Arduino port, so be careful when using any of the untested features.
Some tested features :

- Sending packets uplink, taking into account duty cycling.
- Encryption and message integrity checking.
- Custom frequencies and data rate settings.
- Over-the-air activation (OTAA / joining).
- Receiving downlink packets in the RX1 and RX2 windows.
- MAC command processing

The library has only been tested with LoRaWAN 1.0.2/1.03 networks and is not ready for LoRaWAN 1.1 version.

### 3.3  Run a simple example
RAKwireless provides a lot of example based on this library. Here choose a simple example to show how to develop a LoRaWAN® application.

#### 3.3.1 Scene description
 
The example will communicate with server in EU868, join type in OTAA. And send "Hello_%d" to server period.

#### 3.3.2 Gateway configuration

To test the example, we build node like below in ChirpStack (open-source LoRaWAN® Network Server stack, TTN is also supported and tested).
And we can obtain the DeviceEUI, AppEUI, AppKey, copy them. The detailed parameters is below:

- Region: EU868
- Join Type: OTAA
- Class: A

![4](/RAK4600/image/4.png)

#### 3.3.3 Node configuration
Fill the DEVEUI, APPEUI, APPKEY in the code like below:

```
// This EUI must be in little-endian format, so least-significant-byte
// first. When copying an EUI from ttnctl output, this means to reverse
// the bytes. For TTN issued EUIs the last bytes should be 0xD5, 0xB3,
// 0x70.
static const u1_t PROGMEM APPEUI[8]={0x15,0x09,0x00,0x00,0x00,0x00,0x00,0x00};
void os_getArtEui (u1_t* buf) { memcpy_P(buf, APPEUI, 8);}

// This should also be in little endian format, see above.
static const u1_t PROGMEM DEVEUI[8]={0x15,0x09,0x00,0x00,0x00,0x00,0x00,0x00};
void os_getDevEui (u1_t* buf) { memcpy_P(buf, DEVEUI, 8);}

// This key should be in big endian format (or, since it is not really a
// number but a block of memory, endianness does not really apply). In
// practice, a key taken from ttnctl can be copied as-is.
static const u1_t PROGMEM APPKEY[16] = { 0xb3,0x8b,0x0d,0xef,0x40,0x38,0x31,0x63,0x1f,0x81,0x34,0x8e,0xeb,0xe6,0x9d,0x66 };
void os_getDevKey (u1_t* buf) {  memcpy_P(buf, APPKEY, 16);}
```
Fill in the keys of node on server in little-endian format. Only APPKEY of TTN is big-endian.

## 4. LoRaWAN® Demo example analyze

Most of what happens in this example occurs in the setup(), on_event() and do_send() functions.

### 4.1 Header files

```
#include <lmic.h>
#include <hal/hal.h>
#include <SPI.h>
```

The include files above are necessary for all LoRaWAN® example.

### 4.2 API functions

**void LMIC_reset()**
Reset the MAC state. All session and pending data transfers will be discarded.

### 4.3 Run-time functions

**os_init()**

The project application initializes the run-time environment using the os_init() function.

**void os_runloop()**

Execute run-time jobs from the timer and from the run queues. This function is the main action
dispatcher. It does not return and must be run on the main thread.

**void onEvent (ev_t ev)**

The LMiC library offers a simple event-based programming model where all protocol events are
dispatched to the application’s onEvent()

The implementation of this callback function may react on certain events and trigger new actions
based on the event and the LMIC state. Typically, an implementation processes the events it is
interested in and schedules further protocol actions using the LMIC API. 


### 4.4 Library parameters configuration

The library configuration file is lmic_project_config.h located on your Arduino library folder arduino-lmic/project_config .
You can edit configuration parameters for your project.
The default LoRaWAN version is V1.0.3. Add the line below if you want to select V1.0.2 version.
```
#define LMIC_LORAWAN_SPEC_VERSION   LMIC_LORAWAN_SPEC_VERSION_1_0_2 
```
The radio transceiver for RAK4600 is SX1276.

```
#define CFG_sx1276_radio 1
```
You should select one of the LoRaWAN Region Configuration for your project:
```
// project-specific definitions
//#define CFG_eu868 1
#define CFG_us915 1
//#define CFG_au915 1
//#define CFG_as923 1
// #define LMIC_COUNTRY_CODE LMIC_COUNTRY_CODE_JP	/* for as923-JP */
//#define CFG_kr920 1
//#define CFG_in866 1

```
### 4.5 Library pin map configuration

The lmic_pinmap struct tells the LMIC library how your board is configured. You must declare a variable containing a pin mapping struct in your sketch file.
The names refer to the pins on the transceiver side and the numbers refer to the Arduino pin numbers.

```
struct lmic_pinmap {
    u1_t nss;
    u1_t rxtx;
    u1_t rst;
    u1_t dio[NUM_DIO];
};

// Pin mapping
const lmic_pinmap lmic_pins = {
    .nss = 4,
    .rxtx = LMIC_UNUSED_PIN,
    .rst = LMIC_UNUSED_PIN,
    .dio = {27, 28, 29},
};
```
### 4.6 Software Initialization

This part is LoRaWAN® initialization with user's configuration,join type and callbacks.
First setup() opens the serial console then set up the LMIC (LoraWAN-in-C, formerly LoraMAC-in-C) framework for use with our radio and region-specific radio settings.
If there isn't a transmission job currently running, we're going to prepare the LoRaWAN packet to send to The Things Network. 
This occurs in the do_send() function.
 
```
void setup()
{
// Console init 

	...

 // LMIC init
    os_init();
    // Reset the MAC state. Session and pending data transfers will be discarded.
    LMIC_reset();
    // Start job (sending automatically starts OTAA too)
    do_send(&sendjob);
	...
}
```

Final step in setup() is call do_send() to prepare packet and wait for TX_COMPLETE event.
After the TX_COMPLETE event, a callback is triggered at TX_INTERVAL to send the next packet by calling do_send() again.

```
void do_send(osjob_t* j){
    // Check if there is not a current TX/RX job running
    if (LMIC.opmode & OP_TXRXPEND) {
        Serial.println(F("OP_TXRXPEND, not sending"));
    } else {
        // Prepare upstream data transmission at the next possible time.
        sprintf((char *)lora_data, "Hello_%d", lora_count++);
        LMIC_setTxData2(1, lora_data, strlen((char *)lora_data), LORA_CONFIRMED);
        Serial.println(F("Packet queued"));
    }
    // Next TX is scheduled after TX_COMPLETE event.
}

// TX_COMPLETE event

    case EV_TXCOMPLETE:
        Serial.println(F("EV_TXCOMPLETE (includes waiting for RX windows)"));
        if (LMIC.txrxFlags & TXRX_ACK)
              Serial.println(F("Received ack\r\n"));
        if (LMIC.dataLen) {
            Serial.print(F("Received "));
            Serial.print(LMIC.dataLen);
            Serial.println(F(" bytes of payload"));
        }
            // Schedule next transmission
        os_setTimedCallback(&sendjob, os_getTime()+sec2osticks(TX_INTERVAL), do_send);
        break;
```

### 4.7 loop

The main loop() calls os_runloop_once(), which calls the LMIC runloop processor.
The loop causes radio events to occur based on events and time (callbacks).
The LoRaWAN timing is critical and it's not recommended to place additional code on loop().

```
void loop()
{
	os_runloop_once();
}
```
### 4.8 onEvent

This callback function may react on certain events and trigger new actions based on the event and the LMIC state.
Typically, an implementation processes the events it is interested in and schedules further protocol actions using the LMIC API.
The following callback events will be reported:
```
    switch(ev) {
        case EV_SCAN_TIMEOUT:
            Serial.println(F("EV_SCAN_TIMEOUT"));
            break;
        case EV_BEACON_FOUND:
            Serial.println(F("EV_BEACON_FOUND"));
            break;
        case EV_BEACON_MISSED:
            Serial.println(F("EV_BEACON_MISSED"));
            break;
        case EV_BEACON_TRACKED:
            Serial.println(F("EV_BEACON_TRACKED"));
            break;
        case EV_JOINING:
            //Serial.println(F("EV_JOINING"));
            Serial.println(F("Start Joining..."));
            break;
        case EV_JOINED:
            Serial.println(F("EV_JOINED"));
            {
              u4_t netid = 0;
              devaddr_t devaddr = 0;
              u1_t nwkKey[16];
              u1_t artKey[16];
              LMIC_getSessionKeys(&netid, &devaddr, nwkKey, artKey);
              Serial.print("netid: ");
              Serial.println(netid, DEC);
              Serial.print("devaddr: ");
              Serial.println(devaddr, HEX);
              Serial.print("artKey: ");
              for (size_t i=0; i<sizeof(artKey); ++i) {
                Serial.print(artKey[i], HEX);
              }
              Serial.println("");
              Serial.print("nwkKey: ");
              for (size_t i=0; i<sizeof(nwkKey); ++i) {
                Serial.print(nwkKey[i], HEX);
              }
              Serial.println("");

              lora_count = 1;
            }
            // Disable link check validation (automatically enabled
            // during join, but because slow data rates change max TX
            // size, we don't use it in this example.
            LMIC_setLinkCheckMode(0);
            break;
        /*
        || This event is defined but not used in the code. No
        || point in wasting codespace on it.
        ||
        || case EV_RFU1:
        ||     Serial.println(F("EV_RFU1"));
        ||     break;
        */
        case EV_JOIN_FAILED:
            Serial.println(F("EV_JOIN_FAILED"));
            break;
        case EV_REJOIN_FAILED:
            Serial.println(F("EV_REJOIN_FAILED"));
            break;
        case EV_TXCOMPLETE:
            Serial.println(F("EV_TXCOMPLETE (includes waiting for RX windows)"));
            if (LMIC.txrxFlags & TXRX_ACK)
              Serial.println(F("Received ack\r\n"));
            if (LMIC.dataLen) {
              Serial.print(F("Received "));
              Serial.print(LMIC.dataLen);
              Serial.println(F(" bytes of payload"));
            }
            // Schedule next transmission
            os_setTimedCallback(&sendjob, os_getTime()+sec2osticks(TX_INTERVAL), do_send);
            break;
        case EV_LOST_TSYNC:
            Serial.println(F("EV_LOST_TSYNC"));
            break;
        case EV_RESET:
            Serial.println(F("EV_RESET"));
            break;
        case EV_RXCOMPLETE:
            // data received in ping slot
            Serial.println(F("EV_RXCOMPLETE"));
            break;
        case EV_LINK_DEAD:
            Serial.println(F("EV_LINK_DEAD"));
            break;
        case EV_LINK_ALIVE:
            Serial.println(F("EV_LINK_ALIVE"));
            break;
        /*
        || This event is defined but not used in the code. No
        || point in wasting codespace on it.
        ||
        || case EV_SCAN_FOUND:
        ||    Serial.println(F("EV_SCAN_FOUND"));
        ||    break;
        */
        case EV_TXSTART:
            //Serial.println(F("EV_TXSTART"));
            os_setTimedCallback(&sendjob, os_getTime()+sec2osticks(TX_INTERVAL), do_send);
            break;
        default:
            Serial.print(F("Unknown event: "));
            Serial.println((unsigned) ev);
            break;
    }
```

>LoRa® is a registered trademark or service mark of Semtech Corporation or its affiliates. LoRaWAN® is a licensed mark.