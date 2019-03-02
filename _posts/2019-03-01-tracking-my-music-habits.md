---
layout: post
title: Tracking my music habits
medium_link: none
categories: [c, golang, viz, analytics]
---

This post describes my experiment to track my music listening habits - how long am I exposed to sound, is it loud, and precisely, if I am using my headphones way too much. :worried:

![Demo](https://raw.githubusercontent.com/himanshub16/music-habits/master/preview.gif)

For a lazy sloth like me, who spends hours sitting continuously infront of the computer, music is the way to fill the silence the room.
Lately, on realizing that I am using my headphones a lot more than usual, I decided to track down my habits.

#### Cracking down the problem statement
The problem statement breaks down as follows:
* [Finding a source which can provide all events](#step-1) about - audio played, paused, volume changed, mute, headphones inserted and removed.
* [Capturing all events from that source](#step-2).
* [Aggregating all events to find required metrics](#step-3).
* [Visualize the behaviour pictorially](#step-4).


## Step-1
##### Finding a source which can provide all events

Being on Linux, [pulseaudio](https://wiki.archlinux.org/index.php/PulseAudio) is a perfect source to gather all events mentioned above (6 in number).

Linux follows a client-server architecture when dealing with audio sources. [ALSA](https://wiki.archlinux.org/index.php/Advanced_Linux_Sound_Architecture) is a set of built-in Linux kernel modules which handles sound card drivers with kernel and provides an interface to build applications over it.

Pulseaudio is a sound server whch runs as a middleware between applications and hardware using ALSA. Every application playing some audio opens a stream to pulseaudio server, called **sink-input** and the hardware device is termed as **sink**.

[pactl](https://linux.die.net/man/1/pactl) is a command-line utility which uses pulseaudio to read about its state from command-line. One can try following commands to understand more about `pactl`.
```bash
pactl list sinks
pactl list sink-inputs
pactl subscribe
```

Seems like we have cracked the first step.

## Step-2
##### Capturing all events from pulseaudio
Python has been the go-to language to create scripts for such tasks. A saviour in this case would be a Python wrapper which can help this.
The following python script using [`pulsectl`](https://pypi.org/project/pulsectl/) was my first attempt to gather data.

```python
import pulsectl

with pulsectl.Pulse('log-events') as pulse:
  def print_events(ev):
    print('Pulse event:', ev)

  pulse.event_mask_set('all')
  pulse.event_callback_set(print_events)
  pulse.event_listen(timeout=0)
```

A major drawback with this wrapper is that it doesn't provide the **corked** status on any card. A sink/output is said to be corked when it is not sending any data to the hardware. This happens when you hit pause on the stream and is an important event to capture.

**Solution?**

`pactl` provides `corked` flag in it's result. So, if I can implement a part of what `pactl subscribe` does and log that to a file, then it can help. That would either mean parsing the output on shell, or using the [C API](https://www.freedesktop.org/wiki/Software/PulseAudio/Documentation/).
The second one appears to be a cleaner solution, and some thrill for this idle person who hasn't handled something tiring for a long time.

Dealing with C, with callbacks/async code and strict typing makes things too slow. :sob: After hours of understanding the source of `pactl`, fixing segfaults, linker errors, and further more hours of removal of unrequired code (need subscribe, and callbacks for sink and sink_inputs), I finally arrived at a readable scope of about 500 lines from 2000 lines.

Now, all that is left is to dump the events to a file - CSV format in this case. The events were dumped to the logfile corresponding to the application name.

The second step is also solved now using C.

## Step-3
##### Aggregating all events to find required metrics
This requires reading the CSV file / logfiles created and group the timeseries events based on the presently active device.

Simple loops nah, I used Golang for this. :sunglasses:

## Step-4
##### Visualize the behaviour pictorially
Golang provides a lot of things as an HTTP server along with CSV parser, right there in the standard library.

However, visualisation is much more convient using HTML instead of any other way of generating images (unless I am a pro at aligning text rightly).
You can find the glimpse of the review in the GIF above.


### Contribution
Although, I've tried hard, there are still a few things which could be improved as performing plotting using Golang ([issue](https://github.com/himanshub16/music-habits/issues/1)), preparing a proper Makefile for `pactl.c` ([issue](https://github.com/himanshub16/music-habits/issues/2)).

In case you have any suggestion, alternate implementations, enhancements, feel free to express yourself in the comments, [issues](https://github.com/himanshub16/music-habits/issues/), or a [pull-request](https://github.com/himanshub16/music-habits/pulls/).
