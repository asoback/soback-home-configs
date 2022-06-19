# soback-home-configs
My home assistant  and rhasspy configs, and various other helpful scripts and info for my home setup.

## Setup

### Equipment list

- Raspberry pi 3b+
- ReSpeaker 2 Mics Pi Hat
- Small USB speaker
- Bluetooth speaker

### Raspberry pi setup

I followed these instructions to install the Raspian OS on the pi: https://www.raspberrypi.com/software/

I had installed the ReSpeaker 2 Mics Pi Hat, and the set it up with the following instructions: https://wiki.seeedstudio.com/ReSpeaker_2_Mics_Pi_HAT_Raspberry/

I installed home assistant on the pi with instructions from the README in the `hass/` directory.

I set up rhasspy on the pi as well, but found it to be too slow to be useful. I intend to try again on a second pi and return to update this README. These instructions will be in the `rhasspy/` directory.

I installed `Docker`, following these:  https://rhasspy.readthedocs.io/en/latest/installation/

### Bluetooth

This was an attempt to set up a bluetooth speaker I have.

I was able to hold the power button a few seconds, and it went into pairing mode (flashing light)

https://www.cnet.com/tech/computing/how-to-setup-bluetooth-on-a-raspberry-pi-3/

Following these instrctions, ssh'd into the Pi, I ran: 
```
$ sudo bluetoothctl
agent on
default-agent
scan on
# then I saw the device discovered, and I did
pair XX:XX:XX:XX:XX:XX
connect XX:XX:XX:XX:XX:XX
```

My speaker made a chirp to let me know I had connected

The only way I found so far to change the sound setting was in the Raspian GUI.

Set the volume with `sudo amixer cset numid=1 80%`

## Booting the pi without a GUI
You can also set up a wireless network here.

`$ sudo raspi-config`

*This may not always be the same depending on OS version* 

Wireless:
```
System Options -> Wireless Lan -> configure wireless settings
```

No GUI:
```
Boot/ -> Auto Login -> Console AutoLogin
```

After saving, I am prompted to reboot, and I select yes.
