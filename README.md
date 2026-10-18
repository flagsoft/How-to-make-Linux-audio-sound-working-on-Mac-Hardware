# How to make Linux audio sound working on Apple Mac Hardware
How to make Linux audio sound working on Mac Hardware from Apple.

You can find this information online here: [How-to-make-Linux-audio-sound-working-on-Mac-Hardware](https://github.com/flagsoft/How-to-make-Linux-audio-sound-working-on-Mac-Hardware) 

Last update: 18. OCT. 2026

While sound on a regular PC hardware running Linux seems to work out of the box and not a big problem.
But when it comes to Mac hardware it can be frustrating and quite a bit challenging.



# *** If you have iMac28,2 Retina display on Zorin OS 18.1 with Linux Kernel version 7.0.0-28-generic or similar
(22. AUG. 2026)
Use this script:
https://github.com/flagsoft/iMac181-CS8409-Audio-Fix-Ubuntu/blob/main/ubuntu-26.04-kernel-7.0/install-driver-imac181.sh

It works for iMac18,1 and iMac18,2 models.
NOTE: If you have a more common compatible iMac18,1 non retina display you maybe do not need this kernel module, see below.

It basically downloads Linux kernel source 7.0.0-28-generic then create a .tar.bz2 but only from sound/hda with applied patches for Apple Hardware.
Then it compiles the source into a module.  Next time you reboot it uses this fixed sound module.

It's an update to this file https://github.com/xaviware/iMac181-CS8409-Audio-Fix-Ubuntu/blob/main/ubuntu-26.04-kernel-7.0/install-driver-imac181.sh
of this project: iMac181-CS8409-Audio-Fix-Ubuntu from: [[xaviware](https://github.com/xaviware/)](https://github.com/xaviware/). 



# How to update Linux Kernel on Zorin OS
(18. OCT 2026)
1. You need to run the install-driver-imac181.sh only ONCE. In installs a dynamic kernel module so called DKMS which gets compiled automatically if new kernel installes.

For example, you know have a Linux version 7.0.0-28-generic kernel up and running with sound working.

"Dynamic Kernel Module Support (DKMS) is a program/framework that enables generating Linux kernel modules whose sources generally reside outside the kernel source tree. The concept is to have DKMS modules automatically rebuilt when a new kernel is installed." Source [[Wikipedia](https://en.wikipedia.org/wiki/Dynamic_Kernel_Module_Support)](https://en.wikipedia.org/wiki/Dynamic_Kernel_Module_Support) 

2. Update your Zorin OS as usual

NOTE: Make sure you enable [x] Source code so that Zorin OS can download the Linux source code.

For example, a new source code on Zorin OS is available, linux-image-unsigend-7.0.0-38-generic.

### How to make sure it loads the new kernel first? (GRUB)

```
$ sudo vi /etc/defaults/grub
```
(use vi editor or any other text editor)

Make sure it looks like:
```
GRUB_DEFAULT=0
GRUB_TOP_LEVEL=/boot/vmlinuz-7.0.0-38-generic
GRUB_TIMEOUT_STYLE=menu
GURB_TIMEOUT=10
...
```

Install new boot loader with:

```
$ sudo update-grub
```

Reboot.

After reboot, make sure the right kernel is loaded and in use with:

```
$ uname -a
```
Or just use Settings, About to see that the new kernel is in use.



## *** IF you have iMac28,1

### Step 1: Which sound Chip you are using?

`$ cat /proc/asound/card0/codec* | grep Codec`

It should report your sound chip. For example:

`Codec: Cirrus Logic CS8409`

Note it down here, my sound chip is: _____________. You will need this information later in step 2.



### Step 2: follow the instructions for your specific sound chip 

According to your sound chip, you need to follow different instructions.


#### Sound Chip: Intel HDA?
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



#### Sound Chip: Cirrus Logic CS8409
- Status: works
- Level: not an easy fix
- Known hardware that works: iMac19,2:





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





# FAQ

## How can I download Linux source code Zorin OS?

First make sure you have Source code enabled, so Zorin OS can download it.
> App Software Updater > Settings > enable [x] Source Code.

```
sudo apt-get source linux-image-unsigned-7.0.0-28-generic

sudo apt-get source linux-image-unsigned-7.0.0-30-generic    

sudo apt-get source linux-image-unsigned-7.0.0-38-generic
```

This expands to "./linux-hwe-7.0-7.0.0/"
(The install sound script renames this folder to ./linux-source-7.0.0/)


After install sound script runs successfully you must have this dynamic kernel modul (DKMS):
```
/usr/lib/modules/7.0.0-28-generic/updates/dkms/snd-hda-codec-cs409.ko.zst"
```


## Can I use the same Linux if I changed / added a new support for my sound chip?

Yes. In fact you can use the same Linux configuration and use it with diffrent hardware iMac, MacBook Pro, PC, etc. whatever hardware it will support.

You can for example test your system and boot with Linux on an external USB-disk / SSD.


# Troubleshooting

If something goes wrong.

## Turn ON boot-up messages (so you can see what's going on)

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

Mini `vi` text editor howto:

- `Cursor keys` - move up, down, left and right
- `i` - insert text at current position
- `ESC` - exit text insert mode
- `:wq` - enter command mode and WRITE and QUIT
- `:q!` - enter command mode and QUIT *without* saveing changes


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



### check if sound is there

```
dkms status | grep snd_hda_macbookpro
```

```
aplay -l
```

```
speaker-test -c 2 -t wav
```

```
journalctl -b 0 | grep -i cs8409 | head -10
```
(it must show 'trying APPLE' in the output) 
