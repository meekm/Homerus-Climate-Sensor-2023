# Homerus Climate sensor 2023
These climate sensors are designed for the Homerus-Energiek project in Apeldoorn. They measures temperature, humidity and air quality.
The sensors are mounted in lampposts and communicate via LoRAWan with the TTN network.

## Requirements 
- The sensor measures each 2 minutes temperatue, humidity and particular matter PM1.0, 2.5 and 10
- The sensor sends the measurements to the TTN network using LoRaWan radio
- The sensor can be mounted on lamppost (case IP65)
- The sensor operates on LiPo battery (for minimal 18 hours) and can be charged by 230V supply 
- The sensor battery is charged at night by the lamppost
- GPS location and battery voltage are reported at regular intervals

## Realization

<img src="images/PXL_20240218_104532205.jpg" width="400" >

 Climate Sensor in Kradex housing

 <img src="images/PXL_20240218_104554043.jpg" width="200" >

Air inlet dust sensor, with insect sreen

## Hardware:
- LilyGO TTGO T-Beam - LoRa 868MHz - NEO-M8N GPS - ESP32 
- Sensirion SPS30 Particulate matter sensor i2c
- AM2315C Temperature/humidity sensor i2c
- 18650 Li-ion Batterij - 2600mAh
- Hi-Link PCB Voeding - 5VDC 1A
- Mini DC-DC 5V Step-up Boost Converter 480mA
- PCB for connecting components
- Kradex housing 176x126x57mm - IP65

There are 2 types of TTGO T-Beam versions:
- TTGO T-Beam V1.2 with LoRa SX1262
- TTGO T-Beam V1.2 with LoRa SX1276

For this reason we use the LoRa LacunaSpace basicmac library wich supports both LoRa chips SX1262 and SX1276

## Electronic diagram
<img src="images/Homerus-Hittestress-2023-diagram.png" >


## Software 

### Prerequisites:
- Visual Studio with Platform IO
- LacunaSpace/basicmac LoRa library
- mikalhart/TinyGPSPlus GPS library
- sensirion/sensirion-sps particicle matter SPS30 library
- lewisxhe/AXP202X_Library power control T-Beam
- RobTillaart/AM2315C library for the i2c AM2315C sensor
- espressif32@3.5.0 platform with board ttgo-t-beam

All libraries are defined in platformio.ini and will be loaded automatically when the project is opened, except for the LacunaSpace/basicmac LoRa library.

### Install LacunaSpace/basicmac manually
Download the LacunaSpace/basicmac zip library from https://github.com/LacunaSpace/basicmac.
Run the script ./target/arduino/export.sh or ./target/arduino/export.bat to export the necessary files to a specific location. Specify this location in your platformio.ini file.

## Instructions
### Change the parameters in the basicmap library if necesssary.
In the file hal/target-config.h some parameters are defined, defaults are:
> #define CFG_eu868 1  
> #define BRD_sx1262_radio 1

Change the parameters by (un)commenting those lines accodingly, if you have a sx1276 board or you are outside Europe.

### LoRaWan TTN keys
In the file configuration.h the TTN keys APPEUI an APPKEY are defined.
The unique DEVEUI key is obtained from the T-BEAM board id.

## TTN interface
Two type of messages are sent by the sensor:
- measurement report (each 2 minutes), send on TTN port 15
- status report (eachtime after 100 measurement reports), send on TTN port 16

Measurement report contains:
- temperature in C
- Relative Humidity in %
- Particulate Matter 1.0 μg/m3
- Particulate Matter 2.5 μg/m3
- Particulate Matter 10 μg/m3
- Battery voltage in V

Status report contains:
- GPS position lat/lon, hdop, altitude
- Sensor version
- batterij voltage

The binary messages from the Sensor are converted by the TTN Payloaddecoder into Json.  
Example JSON measurement message:

> {"pm10":8.5,"pm1p0":7.22,"pm2p5":8.02,"rh":99.99,"temp":7.82,"vbat":4.037}

Example JSON status message:
> {"latitude":52.224578,"longitude":6.0061902,"alt":0.0,"hdop":1.61,vbat":4.099,"SwVer":3.02 }


