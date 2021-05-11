# M5Stack-COM.LoRaWAN-Network-Tester

A LoRaWAN Network Tester based on the M5Stack for the COM.LoRaWAN module, compatible with TTN (The Things Network). Currently i am only supporting TTN V2, i am testing with The Things Stack (V3), but the node behaves very confusing (sending unwanted/untriggered Uplinks).

The AT command set of the module does not allow to perform a LinkCheckRequest. So in this version LCM and SSV mode are not integrated/possible. Currently i am testing a workarround with Node-RED, creating a new downlink with the needed informations.

To use the new SetDR feature, you have to update the CubeCell module. In short you have to flash the LoRa -> AT_Command example ont the CubeCell module. Don't forget to set the region. Instructions for [Update COM.LoRaWAN] (Only in german). ACK won't work past SF8 because TTN will send on RX2 frequency an SF9.

[Version for LoRa 868 Module]

[Version for LoRaWAN Module]

## Setup
The tester is designed to work with the following hardware:
  - M5Core (Basic, Gray or Fire)
  - M5Go Base (optional)
  - M5Stack GPS Module or Mini GPS/BDS Unit (optional)
  - [M5Stack COM.LoRaWAN Module]

#### Required Libraries!
  - [M5Stack]
  - [TinyGPSPlus]
  - [NeoPixelBus]
  - [M5_UI] - this Fork enables TTN Mapper like colours for RSSI values in the progressbar

 
#### Installation and Configuration
Upload this sketch to your M5 using the Arduino IDE. M5Stack Fire users have to disable PSRAM, because it will interfer with UART2.
UART2 with GPIO 16 and 17 willbe used for the GPS module.

USe the mini switch on dem COM.X module to set RX to 13 an TX to 5.

![Setup Image](https://github.com/Bjoerns-TB/M5Stack-COM-LoRaWAN-Network-Tester/blob/main/images/IMG_2197-scaled.jpg "Module Setup")
   
By commenting out #define M5go it is possible to disable the M5GO Base. This will disable all NeoPixel related code and features.
By commenting out #define M5gps ist is possible to disable the M5GPS module. This will disable all GPS related code and features. 
  
Change the your TTN keys under //ABP and //OTAA inside the initlora() function from the networktester.ino file. If you want yo use OTAA you have to register a second device for your application. 

Payload Decoder for TTN (V2) (also compatible with TTN Mapper integration):

```
function Decoder(b, port) {

  var lat = (b[0] | b[1]<<8 | b[2]<<16 | (b[2] & 0x80 ? 0xFF<<24 : 0)) / 10000;
  var lon = (b[3] | b[4]<<8 | b[5]<<16 | (b[5] & 0x80 ? 0xFF<<24 : 0)) / 10000;
  var alt = (b[6] | b[7]<<8 | (b[7] & 0x80 ? 0xFF<<16 : 0)) / 100;
  var hdop = b[8] / 10;

  return {
    
      latitude: lat,
      longitude: lon,
      altitude: alt,
      hdop: hdop
    
  };
}
```
Payload Decoder for The Things Stack (V3) (experimental):
```
function decodeUplink(input) {
    var data = {};

  data.latitude = (input.bytes[0] | input.bytes[1]<<8 | input.bytes[2]<<16 | (input.bytes[2] & 0x80 ? 0xFF<<24 : 0)) / 10000;
  data.longitude = (input.bytes[3] | input.bytes[4]<<8 | input.bytes[5]<<16 | (input.bytes[5] & 0x80 ? 0xFF<<24 : 0)) / 10000;
  data.altitude = (input.bytes[6] | input.bytes[7]<<8 | (input.bytes[7] & 0x80 ? 0xFF<<16 : 0)) / 100;
  data.hdop = input.bytes[8] / 10;

        return {
        data: data,
        warnings: [],
        errors: []
    };
}
```

## Instructions for Use

#### Menu

On boot you will be presented with the "Boot-Logo" followed by the first working mode. At the moment the tester has 5 modes to select:
  - [NACK](#nack) 
  - [ACK](#ack)  
  - [MAN](#man) 
  - [LCM](#lcm) 
  - [OTAA](#otaa)
  - [SET](#set)
 
You can move between menu items by pushing the button A. 

![Menu Image](https://github.com/Bjoerns-TB/M5Stack-LoRaWAN-Network-Tester/blob/master/images/menu.jpg "Fig 1. Menu")
  
#### NACK 
#### (No Acknowladge)
"NACK" is a mode that utlises the current device [settings](#set) to perform periodic transmissions. "NACK" mode is great for use with TTN Mapper.

![NACK Image](https://github.com/Bjoerns-TB/M5Stack-LoRaWAN-Network-Tester/blob/master/images/nack.jpg "Fig 2. NACK")

By pushig button C the display and LEDs will be turned off. Pushing button C again will turn them on.

#### ACK 
#### (Acknowladge)
"ACK" will perform the same test as NACK but it will request an ACK for every transmission. The RSSI and SNR values of the received packet will be shown on the display. By pushig button C the display and LEDs will be turned off. Pushing button C again will turn them on.

![ACK Image](https://github.com/Bjoerns-TB/M5Stack-LoRaWAN-Network-Tester/blob/master/images/ack.jpg "Fig 3. ACK")

#### MAN 
#### (Manual)
"MAN" will send a LoRaWAN packet with ACK by pushing button C.

![MAN Image](https://github.com/Bjoerns-TB/M5Stack-LoRaWAN-Network-Tester/blob/master/images/man.jpg "Fig 4. MAN")

#### LCM 
#### (LinkCheckMode) - Currently only a teaser, code not implemented yet; Node-RED needed
"LCM" is a mode that will trigger a LinkCheckRequest, with the help from Node-RED. For this to work you will need a running instance of Node-RED using this [Flow]. Pushing button B will let you cycle through each spreadfactor. The request is triggered by button C. The Uplink data will be presented in te follwoing order: No of Gateways | RSSI | SNR

![LCM Image](https://github.com/Bjoerns-TB/M5Stack-COM-LoRaWAN-Network-Tester/blob/main/images/IMG_2500.jpg "Fig 5. LCM")

#### OTAA 
#### (OverTheAirActivation)
"OTAA" enables the tester to perform OTAA-Joins. By selecting Join the tester will try to join the TTN Network. After an successful the the tester will start with periodic transmissions. You have the choice between transmission with or without ACK. The RSSI and SNR values of the received packet will be shown on the display. If there is no valid GPS fix, a packet can by manually send by pushing button B.

![OTAA Image](https://github.com/Bjoerns-TB/M5Stack-LoRaWAN-Network-Tester/blob/master/images/otaa.jpg "Fig 7. OTAA")

#### SET 
#### (Settings)

"SET" allows to change the transmission intervall in NACK or ACK mode. Possible settings are 15/30/45/60/120 seconds. Pressing button C will active the powersaving mode. The node will go to deep sleep and wakes up according to the transmission intervall. Sleep mode can only be stopped by resetting the devicde.

![SET Image](https://github.com/Bjoerns-TB/M5Stack-LoRaWAN-Network-Tester/blob/master/images/set.jpg "Fig 7. SET")

## Notes
  - If you have a valid GPS fix the GPS track will be written to the SD card as a GPX file.
  - Periodic transmission will only work with a valid GPS fix and an GPS age below 2 seconds
  
  
## Known Bugs/Limitations
  - No LinkCheckRequest possible (curently testing an workarround with Node-RED, generating a downlink with the informations.)
  

## Accesories
  - Michael designed a [Case]. Thank you!
  
  
## Changelog

  - 11.05.2021
    - Ad support for SetDR command (the CubeCell must be updatet to AT Comannd V1.3)
    - Remove debug code
    - Fixed beep only on ACK received
    - Changed status message for no ACK received to "ACK NOT OK"
    - Added The Things Stack payload decoder
    - Turn off LED for RSSI if no ACK received
    
  - 01.03.2021
    - First commit


## ToDo
  - improve powersave features (GPS module)


[M5Stack]: https://github.com/m5stack/M5Stack
[TinyGPSPlus]: https://github.com/mikalhart/TinyGPSPlus
[NeoPixelBus]: https://github.com/Makuna/NeoPixelBus
[M5_UI]: https://github.com/Bjoerns-TB/M5_UI/tree/TTN-Mapper-Colours-Progressbar
[geojson.io]: http://geojson.io/
[M5Stack COM.LoRaWAN Module]: https://m5stack.com/products/com-lorawan-module-868mhz-asr6501
[Case]: https://www.thingiverse.com/thing:4706335
[Version for LoRa 868 Module]: https://github.com/Bjoerns-TB/M5Stack-LoRa-868-Network-Tester
[Version for LoRaWAN Module]: https://github.com/Bjoerns-TB/M5Stack-LoRaWAN-Network-Tester
[Update COM.LoRaWAN]: https://www.bjoerns-techblog.de/2021/05/update-des-com-lorawan/
[Flow]: https://github.com/Bjoerns-TB/M5Stack-COM-LoRaWAN-Network-Tester/blob/main/node-red/flows.json
