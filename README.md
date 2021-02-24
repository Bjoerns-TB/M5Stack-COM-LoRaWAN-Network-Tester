# M5Stack-COM.LoRaWAN-Network-Tester

A LoRaWAN Network Tester based on the M5Stack for the COM.LoRaWAN module, compatible with TTN (The Things Network)

The AT command set of the module does not allow the change of the spreading factor and it is not possible to perform a LinkCheckRequest. So this version does not allow you to change the SF also LCM and SSV mode are not integrated/possible.

So at the momenat the tester ist fixed to SF7 and this repo is only seen as a proof of concept.

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

Payload Decoder for TTN (also compatible with TTN Mapper integration):

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

## Instructions for Use

#### Menu

On boot you will be presented with the "Boot-Logo" followed by the first working mode. At the moment the tester has 7 modes to select:
  - [NACK](#nack) 
  - [ACK](#ack)  
  - [MAN](#man)  
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
  - No change of SF supported
  - No LinkCheckRequest possible
  

## Accesories
  - Michael designed a [Case]. Thank you!
  
  
## Changelog
    
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
