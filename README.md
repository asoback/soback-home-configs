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

# Setting up the libre computer board

I installed debian 9 stretch with Etcher

https://libre.computer/products/s905x/

The username and password are in the README

```
sudo apt install network-tools
sudo apt install net-tools
sudo apt install hddtemp lm-sensors # to keep track of temps
```


`sudo vim /etc/dhcpcd.conf`


# Pihole

I ran the command `curl -sSL https://install.pi-hole.net | bash` as instructed by the [official website](https://github.com/pi-hole/pi-hole/#one-step-automated-install)

Once it finished, I got a message with the webpage URL, the admin password, and some meta data. I copied this to a note, so that I wouldn't forget it.

The URL was http://localhost/admin/ for my Pi.

Because I have a uverse router that doesn't let you put a DNS server behind it, I had to configure each device I wanted to use it. I went to WiFi Settings on my laptops, found my home WiFi name, clicked the Settings icon, and then changed the DNS field from my routers IP address to my Pi IP address.