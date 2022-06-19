## Page Status

Draft

## Connecting to an Arduino

(and a few other small electronics parts)

## ESP WROOM 32

I had an ESP32 that I wanted to use with some of hobby electronics parts I had laying around. I stuck a few things into a breadboard, and installed the arduino IDE.

On my local machine, I installed the `arduio` IDE from `apt`, but I did not get the latest version, at least on this version of ubuntu I am running on my laption. I got the latest version straight from the source/

https://randomnerdtutorials.com/installing-the-esp32-board-in-arduino-ide-windows-instructions/

Also install `python3-pip`

Flash the ESP32 with a config file. This set of components was going to go in my living room, so I named my file `livingroom.yaml`

See that same file in this directory for the whole thing.

To get the ESP32 connected to my laptop, I plugged it in with a USB cord. At first I used a cord that didn't work - some USB cords are for power only! I switched cords and ran `lsusb` and saw it right away. Can also see it with `ls /dev/tty*`


https://esphome.io/guides/getting_started_command_line.html

```
docker pull esphome/esphome
```

It notifies that it started a webserver when run interactively.

More resources that helped me at this stage:

- https://esphome.io/guides/installing_esphome.html
- https://esphome.io/devices/nodemcu_esp32.html
- https://esphome.io/guides/configuration-types.html#config-time

`esphome run livingroom.yaml`

This flashes `livingroom.yaml` to the microcontroller.
Unplug from the computer, and pulg into the wall. 

#### Home Assistant

https://esphome.io/guides/getting_started_hassio.html

I added ESPHome to my home assistant under integrations.

A minute later, i see it discovered on the intefgrations page in ha

I added it.

## Thermometer
My thermometer says on it TMP and has 36GZ.
- https://www.adafruit.com/product/165
- https://learn.adafruit.com/tmp36-temperature-sensor
- https://esphome.io/components/sensor/adc.html
- https://learn.adafruit.com/tmp36-temperature-sensor/using-a-temp-sensor

On source suggested `Temp °C = 100*(reading in V) - 50`
Another suggested `Temp °F = (((x * 3.9/4095)* 1000 - 500 ) /100 ) * 9/5 + 32`

TODO

```
sensor:
  - platform: adc
    pin: 35
    raw: true
    name: "thermometer"
    update_interval: 5s
    filters:
      - lambda: return (((x * 3.9/4095)* 1000 - 500 ) /100 ) * 9/5 + 32;
```

## Photoresistor

https://esphome.io/components/sensor/adc.html

```
sensor:
  - platform: adc
    pin: GPIO32
    name: "photoresistor"
    update_interval: 2s
```
  
## Buzzer

TODO

## Motion Sensor

The motion sensor is a "binary sensor" in home assistant.

I have an old radioshack sensor, it needs 5v. Some of my other items need to be on 3.3v.

## LED

Useful links
- https://esphome.io/components/output/ledc.html
- https://esphome.io/api/namespaceesphome_1_1output.html