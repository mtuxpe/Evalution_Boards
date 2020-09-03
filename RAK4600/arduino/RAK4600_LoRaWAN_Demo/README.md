# RAK4600 LoRaWan® Demo example

## 1. Overview

This example shows how to setup RAK4600 LPWAN Evaluation Board as a LoRaWan® sensor node.
It sends a valid LoRaWAN packet with payload "Hello_%d", using frequency and encryption settings matching those of the The Things Network. 
The parameter %d is the packet counter.
If you want to use LoRaWan® communication protocol you need to define a region. 
The defined region tells the library which frequency and which channels should be used. Valid regions are:

- REGION_AS923 -> Asia 923 MHz
- REGION_AU915 -> Australia 915 MHz
- REGION_EU868 -> Europe 868 MHz
- REGION_IN865 -> India 865 MHz
- REGION_KR920 -> Korea 920 MHz
- REGION_US915 -> US 915 MHz

To use this sketch, first register your application and device with the things network, to set or generate an AppEUI, DevEUI and AppKey.
Multiple devices can use the same AppEUI, but each device has its own DevEUI and AppKey.

OTAA
- Device EUI if you want to use ABP registration of the device
- Application EUI
- Application Key, the AES encryption/decryption cipher application key

The devices must be registered on your LoRaWan® server before they can send and receive data. The above mentioned EUIs and keys can be obtained from your LoRaWan® server. If you use a RAK gateway with an integrated LoRaWan® server or want to use TheThingsNetwork server you can find a tutorial [here](https://doc.rakwireless.com/rak7258-micro-gateway/overview)     

## 2. Hardware Required

- RAK4600 Evaluation Board
- Valid LoRa gateway

## 3. Software Required

### 3.1 Install LoRaWAN library

After install Arduino IDE and BSP according to the Quick Start Guide, you can install LoRaWAN® library now. Step as below:

- Start the Arduino IDE
- Open Library Manager: Tools->Manage Library and search for MCCI LoRaWan LMIC
- Install MCCI LoRaWan LMIC if not installed.

![mcci_library](/RAK4600/image/mcci_library.png)

>Note: This library requires Arduino IDE version 1.6.6 or above, since it requires C99 mode to be enabled by default.

### 3.2 Feature of Arduino-LMIC library

The Arduino-LMIC takes care of all logical MAC states and timing constraints and drives the Semtech SX1276 LoRa radio.
Only a limited number of features was tested on Arduino port, so be careful when using any of the untested features.
Some tested features :

- Sending packets uplink, taking into account duty cycling.
- Encryption and message integrity checking.
- Custom frequencies and data rate settings.
- Over-the-air activation (OTAA / joining).
- Receiving downlink packets in the RX1 and RX2 windows.
- MAC command processing

### 3.3 Run a simple example

RAKwireless provides a lot of example based on this library. Here choose a simple example to show how to develop a LoRaWAN® application.

#### 3.3.1 Scene description
 
The example will communicate with server in EU868, join type in OTAA. And send "Hello_%d" to server period.

#### 3.3.2 Gateway configuration

To test the example, we build node like below in ChirpStack (open-source LoRaWAN® Network Server stack, TTN is also supported and tested). And we can obtain the DeviceEUI, AppEUI, AppKey, copy them. The detailed parameters is below:

- Region: EU868
- Join Type: OTAA
- Class: A

![4](res/4.png)

#### 3.3.3 Node configuration

Then open the example in Arduino, fill the DeviceEUI, AppEUI, AppKey in the code like below:

![5](res/5.png)

#### 3.3.4 Compile and download

There is a USB port on WisBlock. It is a debug port for RAK4631 with three function:

- Power supply
- Download port in Arduino
- Communication with RAK4631, incluing log print and command from PC

Connect WisBlock to PC via USB. Arduino will recognize the board. The red led is on means power up. Go into tools and choose like below. 

![6](res/6-1594607481721.jpg)

Then click Upload (arrow button), it will compile and download to RAK4600 automatically.

![7](res/7.png)

#### 3.3.5 Log print and server data

Go into Tools, choose the port (Adafruit Feather nRF52840 Express), open Serial Monitor. Log will show as below. It will join automatically and send 'hello!' to server per 20s :

![8](res/8.png)

At the same time, server will show the data from RAK4631.

![9](res/9.png)

It is a very easy way for users to finish their first LoRaWAN® trip. But most users want to apply LoRaWAN® to their own application. RAKwireless also provide a efficient way for them.

### 3.4 LoRaWAN® example analyze

Here will analyze the example above in details, other examples are based on this.

#### 3.4.1 Header files

```
#include <lmic.h>
#include <hal/hal.h>
#include <SPI.h>
```

The include files above are necessary for all LoRaWAN® example.

#### 3.4.2 LoRaWAN® software configuration 

```

```

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

#### 3.4.4 User callbacks

```
`
#### 3.4.5 OTAA  Keys

```
//OTAA keys
uint8_t nodeDeviceEUI[8] = {0x88, 0x88, 0x88, 0x88, 0x88, 0x88, 0x22, 0x22};
uint8_t nodeAppEUI[8] = {0xB8, 0x27, 0xEB, 0xFF, 0xFE, 0x39, 0x00, 0x00};
uint8_t nodeAppKey[16] = {0x88, 0x88, 0x88, 0x88, 0x88, 0x88, 0x88, 0x88,0x88, 0x88, 0x88, 0x88, 0x22, 0x22, 0x22, 0x22};
```

Fill in the keys of node on server in MSB.  Only AppKey of TTN is LSB.

#### 3.4.6 Hardware initialization

```
void setup()
{
	...
    // LMIC init
    os_init();
    // Reset the MAC state. Session and pending data transfers will be discarded.
    LMIC_reset();
	...
	

	...
}
```



#### 3.4.7 Software initialization



This part is LoRaWAN® initialization with user's configuration,join type and callbacks.

#### 3.4.8 Join

```
void setup()
{
	...

	...
}
```

Final step in setup() is join to server.

#### 3.4.9 loop

```
void loop()
{
	os_runloop_once();
}
```

The main loop

#### 3.4.10  Join callback

```
void lorawan_has_joined_handler(void)
{
  Serial.println("OTAA Mode, Network Joined!");

  lmh_error_status ret = lmh_class_request(gCurrentClass);
  if(ret == LMH_SUCCESS)
  {
    delay(1000);
  	TimerSetValue(&appTimer, LORAWAN_APP_INTERVAL);
  	TimerStart(&appTimer);
  }
}
```

If node joins successfully, the stack will call this. User can add what they need here. For example, call lmh_class_request to change Class type. In this example, it starts the timer created before to send data.

#### 3.4.11 Receive callback

```
void lorawan_rx_handler(lmh_app_data_t *app_data)
{
	Serial.printf("LoRa Packet received on port %d, size:%d, rssi:%d, snr:%d, data:%s\n",
				  app_data->port, app_data->buffsize, app_data->rssi, app_data->snr, app_data->buffer);

}
```

If node receive data from server, the stack will call this. User can add what they need here. For example, print receive data, RSSI and SNR.

#### 3.4.12 Class confirm callback

```
void lorawan_confirm_class_handler(DeviceClass_t Class)
{
    Serial.printf("switch to class %c done\n", "ABC"[Class]);
    // Informs the server that switch has occurred ASAP
    m_lora_app_data.buffsize = 0;
    m_lora_app_data.port = gAppPort;
    lmh_send(&m_lora_app_data, gCurrentConfirm);
}
```

This callback coordinates with lmh_class_request in Class C. When node needs change to Class C, this will be called after  Class C exchange. And send a uplink to server. Notify server node has changed OK.

#### 3.4.13 Send data

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

```

This function will fill in the packet and send data to server. m_lora_app_data is from LoRaWAN® library and size is no more than 242 bytes. For example, User can call this in a timer handle. 

#### 3.4.14 Complete code

​	

### 3.5 Parameters configuration

For users, application is different and complicated. Although RAKwireless provide abundant application examples, it can't satisfy every people. In allusion to this, RAKwireless provides a efficient way to config all parameters about LoRaWAN®.

#### 3.5.1 Region configuration

As the example shows, its default region is EU868. User can change it in Arduino like below:

![6](res/6-1594607481721.jpg)

#### 3.5.2 OTAA/ABP configuration


#### 3.5.3 Dutycycle configuration

In *.ino,  fill in with **LORAWAN_DUTYCYCLE_OFF** or **LORAWAN_DUTYCYCLE_ON**

```
static lmh_param_t lora_param_init = {LORAWAN_ADR_ON , LORAWAN_DATERATE, LORAWAN_PUBLIC_NETWORK, JOINREQ_NBTRIALS, LORAWAN_TX_POWER, LORAWAN_DUTYCYCLE_OFF};

```

#### 3.5.4 Network configuration

In *.ino,  fill in with **LORAWAN_PUBLIC_NETWORK** or **LORAWAN_PRIVAT_NETWORK**

```
static lmh_param_t lora_param_init = {LORAWAN_ADR_ON , LORAWAN_DATERATE, LORAWAN_PUBLIC_NETWORK, JOINREQ_NBTRIALS, LORAWAN_TX_POWER, LORAWAN_DUTYCYCLE_OFF};
```

#### 3.5.5 Keys configuration

So if want to develop own application, better based on examples of RAKwireless in https://github.com/RAKWireless/WisBlock/tree/master/examples/communications/LoRa/LoRaWAN/ . Open in Arduino, change the OTAA/ABP keys according to user's server configuration (TTN and ChirpStack are same with MSB first, only AppKey of TTN is LSB. This part should be pay more attention):

![14](res/14.png)

#### 3.5.6 ADR configuration

In *.ino,  fill in with **LORAWAN_ADR_ON** or **LORAWAN_ADR_OFF**

```
static lmh_param_t lora_param_init = {LORAWAN_ADR_ON , LORAWAN_DATERATE, LORAWAN_PUBLIC_NETWORK, JOINREQ_NBTRIALS, LORAWAN_TX_POWER, LORAWAN_DUTYCYCLE_OFF};
```

#### 3.5.7 Datarate configuration

Same in Arduino project, *.ino. Datarate can be set from **DR_0** to **DR_5**:

#define LORAWAN_DATERATE  DR_0 /*LoRaMac datarates definition, from DR_0 to DR_5*/

#### 3.5.8 Tx power configuration

Same in Arduino project, *.ino. Tx power can be set from **TX_POWER_0** to **TX_POWER_15**:

#define LORAWAN_TX_POWER TX_POWER_5 /*LoRaMac tx power definition, from TX_POWER_0 to TX_POWER_15*/

#### 3.5.9 Class configuration

Same in Arduino project, *.ino. Class type  can be set to **CLASS_A** or **CLASS_C**:

DeviceClass_t gCurrentClass = CLASS_A; /* class definition*/

Remember to set node application to Class C in the TTN or ChirpStack when set to Class C.

#### 3.5.10 Packet type configuration

Same in Arduino project, *.ino. Packet type  can be set to **LMH_CONFIRMED_MSG** or **LMH_UNCONFIRMED_MSG**:

lmh_confirm gCurrentConfirm = LMH_CONFIRMED_MSG; /* confirm/unconfirm packet definition*/

#### 3.5.11 Packet port configuration

Same in Arduino project, *.ino. LoRaWAN application port  can be set from **0~255** (do not use 224. It is reserved for certification):

uint8_t gAppPort = 2;   /* data port*/


>LoRa® is a registered trademark or service mark of Semtech Corporation or its affiliates. LoRaWAN® is a licensed mark.