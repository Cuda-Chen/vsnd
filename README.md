# Virtual Soundcard Driver for Linux Kernel

`vsnd` implements a Linux device driver that introduces a virtual soundcard.
Typically, a sound card is a hardware component that enables computers to play
audio files. This virtual soundcard, however, is designed to transmit audio
PCM data received from various programs directly into a FIFO file.

## Prerequisites
The following packages must be installed before building `vsnd`.

To compile the kernel driver successfully, package versions of currently used
kernel, kernel-devel and kernel-headers need to be matched.
```shell
$ sudo apt install linux-headers-$(uname -r)
```

Additional packages are required for verification purpose.
```shell
$ sudo apt install alsa-utils ffmpeg
```

## Build and Run
After running make, you should be able to generate the file `vsnd.ko`.

Before loading this kernel module, you have to satisfy its dependency:
```shell
$ sudo modprobe snd_pcm
```

A FIFO file is required during kernel module initialization and is used for
transmitting audio PCM data.
```shell
$ mkfifo /tmp/audio.pcm
```

The module can be loaded to Linux kernel by runnning the command:
```
$ sudo insmod vsnd.ko out_fifo_name=/tmp/audio.pcm
```

Then, use [aplay](https://manpages.org/aplay) to check the soundcard device
provided by `vsnd`.
```shell
$ aplay -l
```

Reference output:
```
card 0: vsnd [vsnd], device 0: vsnd PCM [vsnd PCM]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
```

See [scripts/verify.sh](scripts/verify.sh) for automated test.

### Close the module
- https://stackoverflow.com/a/26860566
- https://askubuntu.com/a/1226710
- https://rastating.github.io/setting-default-audio-device-in-ubuntu-18-04/

```
Card #3
        Name: alsa_card.platform-vsnd.0
        Driver: module-alsa-card.c
        Owner Module: 28
        Properties:
                alsa.card = "1"
                alsa.card_name = "vsnd"
                alsa.long_card_name = "vsnd1"
                alsa.driver_name = "vsnd"
                device.bus_path = "platform-vsnd.0"
                sysfs.path = "/devices/platform/vsnd.0/sound/card1"
                device.form_factor = "internal"
                device.string = "1"
                device.description = "Built-in Audio"
                module-udev-detect.discovered = "1"
                device.icon_name = "audio-card"
        Profiles:
                output:mono-fallback: Mono Output (sinks: 1, sources: 0, priority: 100, available: yes)
                off: Off (sinks: 0, sources: 0, priority: 0, available: yes)
        Active Profile: output:mono-fallback
        Ports:
                analog-output: Analog Output (type: Analog, priority: 9900, latency offset: 0 usec, availability unknown)
                        Part of profile(s): output:mono-fallback
```

## License
`vsnd`is released under the MIT license. Use of this source code is governed by
a MIT-style license that can be found in the LICENSE file.

## Reference
* [The ALSA Driver API](https://www.kernel.org/doc/html/latest/sound/kernel-api/alsa-driver-api.html)
