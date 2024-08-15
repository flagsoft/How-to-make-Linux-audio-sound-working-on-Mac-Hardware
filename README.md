# How to make Linux audio sound working on Mac Hardware
How to make Linux audio sound working on Mac Hardware

You can find this information online here: [How-to-make-Linux-audio-sound-working-on-Mac-Hardware](https://github.com/flagsoft/How-to-make-Linux-audio-sound-working-on-Mac-Hardware) 

Last update: 15. AUG. 2024

While sound on a regular PC hardware running Linux seems to work out of the box and not a big problem.
But when it comes to Mac hardware it can be frustrating and quite a bit challenging.



## Step 1: Turn ON boot-up messages (so you can see what's going on)

You need to change the boot manager.

### How to change and edit GRUB the Linux boot manager
If you need to update or change the Linux boot manager GRUB.

Use `nano` text editor if you are not familiar with editor `vi`.

If you prefere to edit with `nano` text editor use:

```bash
$ sudo nano /etc/default/grub
# -- do your changes as required and save the file
$ sudo update-grub
```

If you prefere to edit with `vi` text editor use:

```bash
$ sudo vi /etc/default/grub
# -- do your changes as required and save the file
$ sudo update-grub
```


Change this:
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash nomodeset"
```

To this:
```
GRUB_CMDLINE_LINUX_DEFAULT="splash nomodeset"
```

`nomodeset` - will disable the intel graphics features which can be sometimes a problem when your system does not boot.

`quiet splash` - this will not show text output, instead a custom graphical logo.


Then shut down your computer with your graphical user interface, or type in `reboot` to restart your computer.





## Step 2: which sound Chip you are using?


`$ cat /proc/asound/card0/codec* | grep Codec`

It should report your sound chip. For example:

`Codec: Cirrus Logic CS8409`

Not is down, my sound chip is: _____________. You will need this information in step 2.



## Step 3: follow the instructions for your specific sound chip 

According to your sound chip, you need to follow different instructions.


### Sound Chip: Intel HDA?
Known hardware to work: iMac??,?
Level: easy

Most of the time there is no sound card listed, with:
```bash
$ cat /proc/asound/cards
--- no soundcards ---
```

Activate the sound card with:
```bash
$ sudo /sbin/modprobe snd-hda-intel model=imac27
```


Check if your sound card is now listed now with:
```bash
$ cat /proc/asound/cards
 0 [PCH              ]: HDA-Intel - HDA Intel PCH
                        HDA Intel PCH at 0x......... irq 63
 1 [HDMI             ]: HDA-Intel - HDA ATI HDMI
                        HDA ATI HDMI at 0x.......... irq 64
```

Thurn UP VOLUME sliders!

You should hear audio now. Playing youtube video, etc.


Make this change permanent with:

```bash
$ sudo vi /etc/modprobe.d/alsa-base.conf
```

At the beginning at this line:

```
install /sbin/modprobe snd-hda-intel model=imac27
```

Save the file.
After that change your sound should work every restrat of your computer as expected.



### Sound Chip: Cirrus Logic CS8409
Status: works
Level: not an easy fix
Known hardware that works: iMac19,2

Codes: **Cirrus Logic CS8409**

You need to compile and install a Linux kernel module by yourself. 
[https://www.github.com/davidjo/snd_hda_macbookpro](https://github.com/davidjo/snd_hda_macbookpro)


#### How to fix Sound distorted in macOS (restart coreaudio)
Problem
- Sound distorted after reboot back to macOS (how to restore macOS sound coreaudio)

Solution: Reset your sound system on macOS.

Restart your iMac, with Power off. I did a test, with Power OFF (plug removed from the main outlet), wait about 10 seconds, and reboot to macOS again.
And the sound seems to be normal. Not shure if this work with a MacBook.

Sound distorted after reboot back to macOS.

Have to RESET macOS sound sytem.

macOS reset sound

`% sudo pkill coreaudiod`

or

`% sudo pkill -9 coreaudiod`

or

You can kill the CoreAudio process by opening Terminal and running:

`% sudo kill -9 ps ax|grep 'coreaudio[a-z]' | awk '{print $1}'`

It will restart automatically after a couple seconds.

