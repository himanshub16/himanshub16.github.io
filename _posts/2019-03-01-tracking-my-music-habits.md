---
layout: post
title: Tracking my music habits
medium_link: none
categories: [c, golang, viz, analytics]
---

This post describes my [experiment](https://github.com/himanshub16/music-habits) to track my music listening habits - how long am I exposed to sound, is it loud, and precisely, if I am using my headphones way too much. :worried:

{% raw %}
<center>
<h3><a href="https://himanshub16.github.io/music-habits">Try this interactive demo</a></h3>
<br>
<img src="https://github.com/himanshub16/music-habits/raw/master/preview.gif" />
</center>
<!-- <iframe frameborder="no" border="1" marginwidth="0" marginheight="0" width="750px" height="650px" src="http://himanshub16.github.io/music-habits"></iframe> -->
{% endraw %}

The data speaks some pretty-obvious facts about me as
- I use laptop speakers lot more than headphones. Living in a single room in hostel gives the freedom to play music without headphones. :speaker:
- For yesterday, my listening time shot to more than 6 hours. That's because I've been sitting whole day writing the code and content for this blog, with youtube playing in the background.
- Hour-wise view shows that usage of headphone is pretty dominant in late night hours (9 PM to 3 AM). That is to keep the noise to myself and not disturb the sleep of people sleeping next door.


### Movtivation
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

**What's pulseaudio?**

Linux follows a client-server architecture when dealing with audio sources. [ALSA](https://wiki.archlinux.org/index.php/Advanced_Linux_Sound_Architecture) is a set of built-in Linux kernel modules which handles sound card drivers with kernel and provides an interface to build applications over it.

Pulseaudio is a sound server whch runs as a middleware between applications and hardware using ALSA. Every application playing some audio opens a stream to pulseaudio server, called **sink-input** and the hardware device is termed as **sink**.

So, if you have two tabs in Chrome with YouTube playing with headphones connected, the active sink is *headphones* and there are at least two sink-inputs - one for either source streaming to pulseaudio server.

As a part of pulseaudio suite, [pactl](https://linux.die.net/man/1/pactl) is a command-line utility which uses pulseaudio to read about its state from command-line. One can try following commands to understand more about `pactl`.
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

Dealing with C, with callbacks/async code and strict typing makes development too slow. :sob: After hours of understanding the source of `pactl`, fixing segfaults, linker errors, and further more hours of removal of unrequired code (need subscribe, and callbacks for sink and sink_inputs), I finally arrived at a readable scope of about 500 lines from 2000 lines.

Now, all that is left is to dump the events to a file - CSV format in this case. The events were dumped to the logfile corresponding to the application name.

The volume in the callbacks is calculated by following relation, for both sink and sink-inputs:
```C
// For SINK
sink_vol = (sink_info->mute || sink_info->state != PA_SINK_RUNNING) ?
            0.0f :
            sink_vol_perc / 100.00f;

// For SINK_INPUT
sink_input_vol = (sink_inuut_info->corked || sink_input_info->mute) ?
                  0.0f :
                  sink_input_vol_perc / 100.00f;

net_vol = max_over_all_sink_inputs(sink_vol * sink_input_vol)
```

The second step is also solved now using C.

## Step-3
##### Aggregating all events to find required metrics
This requires reading the CSV file / logfiles created and group the timeseries events based on the presently active device.
Below is a snippet of how the log file looks like,
```CSV
timestamp,sink_input_id,active_port,sink_input_volume,net_vol
1551178007,1740,analog-output-speaker,0.840000,0.495600
1551178007,1740,analog-output-speaker,0.840000,0.478800
1551178197,1740,analog-output-speaker,0.000000,0.000000
1551178197,1740,analog-output-speaker,-1.000000,-0.570000
1551178283,1741,analog-output-speaker,0.000000,0.000000
1551178283,1741,analog-output-speaker,0.000000,0.000000
1551178283,1741,analog-output-speaker,1.000000,0.570000
1551178287,1741,analog-output-headphones,1.000000,0.150000
1551178291,1741,analog-output-headphones,1.000000,0.170000
1551178291,1741,analog-output-headphones,1.000000,0.190000
```

The negative volumes mark that the sink-input has been removed, say it like music stopped, or tab closed.
We need to bring them to the format as below and then perform calculations over it.
```txt
// for analog-output-speaker
1551178007 to 1551178007 - 0.495600
1551178007 to 1551178197 - 0.478800
1551178197 to 1551178197 - 0.000000
1551178197 to 1551178283 - 0.000000
...
1551178283 to 1551178287 - 0.570000

// for analog-output-headphones
1551178287 to 1551178291 - 0.150000
1551178291 to 1551178291 - 0.170000
1551178291 to 1551178291 - 0.190000
```

Simple loops nah, I [used Golang for this.](https://github.com/himanshub16/music-habits/blob/master/generate_report.go) :sunglasses:

## Step-4
##### Visualize the behaviour pictorially
Golang provides a lot of things as an HTTP server along with CSV parser, right there in the standard library.

However, visualisation is much more convient using HTML instead of any other way of generating images (unless I am a pro at aligning text rightly).
You can view it [interactively on this link](https://himanshub16.github.io/music-habits).

Additionally, I have a text-summary available just to get the things right there on the console.

```bash
> go run generate_report.go -logfile \
   ~/Downloads/pulseaudio-new/src/sink_input-firefox.log \
   -duration today
Headphones : 
Used 26 % time.
Average volume : 19 %
Total time : 55 min
Loud for : 11 min

Laptop speaker : 
Used 74 % time.
Average volume : 66 %
Total time : 2 hrs 36 min
Loud for : 1 hrs 28 min
```


### Final words
Although, I've tried hard, there are still a few things which could be improved as performing plotting using Golang ([issue](https://github.com/himanshub16/music-habits/issues/1)), preparing a proper Makefile for `pactl.c` ([issue](https://github.com/himanshub16/music-habits/issues/2)).

In case you have any suggestion, alternate implementations, enhancements, feel free to express yourself in the comments, [issues](https://github.com/himanshub16/music-habits/issues/), or a [pull-request](https://github.com/himanshub16/music-habits/pulls/).
