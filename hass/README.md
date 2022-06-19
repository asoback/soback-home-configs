# Home Assistant Setup

https://www.home-assistant.io/installation/raspberrypi/

```
$ sudo docker pull homeassistant/home-assistant

$ sudo docker run -d  \
    --name homeassistant  \
    --privileged  \
    --restart=unless-stopped  \
    -e TZ=MY_TIME_ZONE  \
    -v ${HOME}/hass:/config  
    --network=host \
    homeassistant/home-assistant
```

In a browser window, navigate to `${PI_ADDRESS}:8123`

I had to go to my name in hass to turn on advanced mode

Configuration file was in ~/hass. Thats what I put when I ran docker.


## cURL

For some reason I was having trouble reaching the endpoints I wanted to, so here are a few things I found that worked.

```
$ curl -X GET -H "Authorization: Bearer ${TOKEN}" \
    http://${PI_ADDRESS}:8123/api/
{"message": "API running."}

# This gets the time
$ curl -X GET -H "Authorization: Bearer ${TOKEN}" \
    -H "content-type: application/json" \
    http://${PI_ADDRESS}:8123/api/states/sensor.time
{"entity_id": "sensor.time", "state": "22:03",
...
}

# This turns off my "Tall Lamp"
$ curl -X POST -H "Authorization: Bearer ${TOKEN}"  -H 'Content-Type: application/json' -d '{ "name": "HassTurnOff", "data": { "name": "Tall Lamp" } }' ${PI_ADDRESS}:8123/api/intent/handle

# This sets a timer for 30 seconds
$ curl -X POST -H "Authorization: Bearer ${TOKEN}"  -H 'Content-Type: application/json' -d '{ "name": "SetTimer", "data": { "seconds": "30" } }' ${PI_ADDRESS}:8123/api/intent/handle

```

## Adding my floorplan to a home assistant dashboard

I found my flooplan online and saved it. I actually opened in it gimp and exported it as a png, after making some modifications to it's size and color.

The "/config" folder often refered to online was in `~/hass/www` on the Pi. I put the pic in there and restarted HA.

Then I could find it at the `${PI_ADDRESS}:8123/local/floorplan.png`

Then I could create a card, using the picture, and set the url to `/local/floorplan.png`

## Tracking network devices
Install `nmap` with `sudo apt-install nmap`

On the HA dashboard, go to **Configuration->Integrations->Add Integration** and make an `nmap tracker` integration.

I looked at the ips of any with idenifying names, and found a cell phone ips in their settings.

`nmap` network scan: `nmap -sn 192.168.1.1-255/32`

*TODO: Finish this section, create aliases for these, persons, and associate to at least say when people are home*

## Sun, moon, and clouds

- https://community.home-assistant.io/t/how-to-add-sunrise-sunset-cards/109545/5
- https://thissmart.house/2021/07/11/severe-weather-alerts-radar-right-in-home-assistant/

Add the moon sensor to `~/hass/sensors.yaml`

```
  - platform: moon
```

Now, it is listed as a sensor in the home assistant dashboard.

I also added sunrise and sunset, 

```
$ cat ~/hass/sensors.yaml
  - platform: moon
  - platform: template
    sensors:
      nextsunrise:
        friendly_name: 'Next Sunrise'
        value_template: >
          {{ as_timestamp(states.sun.sun.attributes.next_rising) | timestamp_custom(' %I:%M %p') | replace(" 0", "") }}
        icon_template: mdi:weather-sunset-up
      nextsunset:
        friendly_name: 'Next Sunset'
        value_template: >
          {{ as_timestamp(states.sun.sun.attributes.next_setting) | timestamp_custom(' %I:%M %p') | replace(" 0", "") }}
        icon_template: mdi:weather-sunset-down
```

## Time and date

```
$ cat ~/hass/sensors.yaml 
  - platform: time_date
    display_options:
      - 'time'
      - 'date'
      - 'date_time'
      - 'date_time_utc'
      - 'date_time_iso'
      - 'time_date'
      - 'time_utc'
      - 'beat'
```

## Stock Tracker

```
$ cat ~/hass/sensors.yaml 
  - platform: avanza_stock
    stock:
      - id: 299683
        name: VOO
      - id: 232136
        name: VTI
```


## Cards

https://github.com/kalkih/mini-graph-card



## Timers

See `intent_script.yaml` and `timers.yaml`

I first added a timer to the dashboard. This one was `timer.timer1`.

`Configuration->Helpers->Add`

In `intent_script.yaml`:

```
SetTimer:
    action:
      - service: timer.start
        target:
          entity_id: timer.timer1
        data_template:
          duration: "{{ hours if hours != NULL else '00' }}:{{ minutes if minutes != NULL else '00'}}:{{ seconds if seconds != NULL else '00' }}"
    speech:
      text: Timer set for {{ hours if hours != NULL }} {{ 'hours' if hours != NULL }} {{ minutes if minutes != NULL }} {{ 'minutes' if minutes != NULL }} {{ seconds if seconds != NULL }} {{ 'seconds' if seconds != NULL }}
```

This actually only creates one timer. It gets reset every time you call this method.
I expect alexa my be creating timers on the fly, but I think my peak use is around 3 timers when I am cooking, so I duplicated some of this in [intent-script.yaml](intent-script.yaml)

I also had to add timers to home assistant, see  [timers.yaml](timers.yaml)

Of course, I also had to include these files in `configuration.yaml`, and then restart home assistant.

```
timer: !include timers.yaml

intent:
intent_script: !include intent_script.yaml
```

For announcing that the timer is finished, I added a shell command to `configuration.yaml`. Maybe there is a better way to do this...

```
shell_command:
  announce_timer_done: "curl -X POST -H 'content-type: audio/wav' http://localhost:12101/api/play-wav --data-binary @'/home/pi/timer_finished.wav'"
```

Suggeted speaker test steps: https://learn.adafruit.com/usb-audio-cards-with-a-raspberry-pi/testing-audio

The alarm sound I used was downloaded like this:
`wget http://wolo-usa.com/365c-stereo.wav`

http://${PI_ADDRESS}:12101/docs/reference/#http-api

```
curl -X POST -H "content-type: audio/wav" http://${PI_ADDRESS}:12101/api/play-wav --data-binary @"/home/pi/365c-stereo.wav" 
```

The speaker I am using is https://www.adafruit.com/product/3369

## Reminders

```
- alias: TrashReminder
  id: TrashReminder
  trigger:
  - platform: time
    at: '19:00:00'
  condition:
  - platform: time
    weekday:
    - thu
    - sun
  speech:
    text: "Take out the trash"
  mode: single
```

## Todo List

TODO (yo dawg)

I untared todolist into `/home/$USER/hass/config/custom_components/todolist`.

Then, I went to `settings->integrations->add integration`, and searched todolist

I had to visit this location to get the option
`http://${PI_ADDRESS}:8123/_my_redirect/config_flow_start?domain=todo_list`
