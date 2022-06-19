# Rhasspy Setup

I pulled the Rhassy container with docker. https://hub.docker.com/r/rhasspy/rhasspy

```
$ docker pull rhasspy/rhasspy
$ docker run -d \
    -p 12101:12101 \
    --name rhasspy \
    --restart unless-stopped \
    -v "$HOME/.config/rhasspy/profiles:/profiles" \
    -v "/etc/localtime:/etc/localtime:ro" \
    --device /dev/snd:/dev/snd \
    rhasspy/rhasspy \
    --user-profiles /profiles \
    --profile en
```

I basically followed these instructions: https://rhasspy.readthedocs.io/en/latest/tutorials/#from-scratch-on-a-raspberry-pi

I found my pi ip address on the command line with `ifconfig`

On my local machine, I navigated in a browser to `${PI_ADDRESS}:12101`

### Home assistant

https://rhasspy.readthedocs.io/en/latest/intent-handling/

### Wake Word
In the rhasspy settings, `http://${PI_ADDRESS}:12101/settings#wake`
I picked `Porcipine`, and I picked the "grapefruit" file.

I wanted to look at performance, so I pulled up two terminals.

Terminal 1:
`docker logs -f rhasspy`

Terminal 2:
`top`

Then when I said 'grapefruit', I heard a beep that indicated it caught the word, and I also saw audio recording in the logs.

Then I asked for the temp, and watched top.
before: almost 1g in use mem, 43m free. 5% cpu total.
during: brief spike to 20% cpu, no noticeable change in memory.


# Timers

I added a timer to my Rhasspy config. I did this in the web page, at `http://${PI_ADDRESS}:12101`

Copied straight from this person: https://github.com/synesthesiam/rhasspy-nlu/blob/master/etc/timer.ini

```
[SetTimer]
two_to_nine = (two:2 | three:3 | four:4 | five:5 | six:6 | seven:7 | eight:8 | nine:9)
one_to_nine = (one:1 | <two_to_nine>)
teens = (ten:10 | eleven:11 | twelve:12 | thirteen:13 | fourteen:14 | fifteen:15 | sixteen:16 | seventeen:17 | eighteen:18 | nineteen:19)
tens = (twenty:20 | thirty:30 | forty:40 | fifty:50)
one_to_nine = (one:1 | <two_to_nine>)
one_to_fifty_nine = (<one_to_nine> | <teens> | <tens> [<one_to_nine>])
two_to_fifty_nine = (<two_to_nine> | <teens> | <tens> [<one_to_nine>])
hour_half_expr = (<one_to_nine>{hours} and (a half){minutes:30})
hour_expr = (((one:1){hours}) | ((<one_to_nine>){hours}) | <hour_half_expr>) (hour | hours)
minute_half_expr = (<one_to_fifty_nine>{minutes} and (a half){seconds:30})
minute_expr = (((one:1){minutes}) | ((<two_to_fifty_nine>){minutes}) | <minute_half_expr>) (minute | minutes)
second_expr = (((one:1){seconds}) | ((<two_to_fifty_nine>){seconds})) (second | seconds)
time_expr = ((<hour_expr> [[and] <minute_expr>] [[and] <second_expr>]) | (<minute_expr> [[and] <second_expr>]) | <second_expr>)

(set | create | add) [a] timer for <time_expr>
```

Looks like there may be more complete stuff here now: https://github.com/rhasspy/rhasspy-nlu/tree/master/rhasspynlu

This was the easy part. The harder part was setting up timers on home assistant. See
[Timers in Home Assistant](../hass/README.md#timers)


### Troubleshooting

In rhasspy, I needed to set my hass URL to http://, rather than just the ip address!