How to make Broadcom Bluetooth working
When you inserting Broadcom Bluetooth device you prefered Linux distribution may not load it properly.
You may list all Bluetooth devices like this:

dmesg | grep -i bluetooth                    # Shows all Bluetooth driver info
dmesg | grep -i bluetooth | grep -i firmware # Shows Bluetooth firmware issues


The output may look like this:

Bluetooth: hci1: BCM: chip id 63
Bluetooth: hci1: BCM20702A
Bluetooth: hci1: BCM20702A1 (001.002.014) build 0000
bluetooth hci1: Direct firmware load for brcm/BCM20702A1-0b05-17cb.hcd failed with error -2
Bluetooth: hci1: BCM: Patch brcm/BCM20702A1-0b05-17cb.hcd not found

As you can see, you need brcm/BCM20702A1-0b05-17cb.hcd firmware. It may be different.

You may download files from here  

Place required .hcd file to /lib/firmware/brcm/ or in chase /lib/firmware/intel etc. and reboot your computer. After reboot you will see that firmware successfully loaded:

Bluetooth: hci1: BCM: chip id 63
Bluetooth: hci1: BCM20702A
Bluetooth: hci1: BCM20702A1 (001.002.014) build 0000
Bluetooth: hci1: BCM20702A1 (001.002.014) build 1467
Bluetooth: hci1: Broadcom Bluetooth Device

Congratulations, now your bluetooth device successfully loaded!  

If you still have issues try this instead

Bluetooth is great for freeing yourself from the tangle of wires (which is great when dealing with kids). It can be a little bit of a pain to setup sometimes. 
This article covers getting Bluetooth working on Debian or Ubuntu. If you’re on Ubuntu or some other Debian derivative, 
the necessary packages are probably already installed. This article was originally written using Debian 10 as a base which should correspond to roughly Ubuntu 14.04 to 19.04 (though some package names may change).

Installing the Necessary Components

Make sure you are using Pulseaudio if you’re following this tutorial. I prefer Alsa, but it just wasn’t worth the headache for me to use Bluetooth. If you want to use Alsa and Bluetooth, see something like this.

Let’s get on to the installation portion (read about apt on Debian and Ubuntu). Pick the commands to use based on whichever GUI you want. If you’re running Gnome, you probably want either blueman (GTK GUI), or gnome-bluetooth (Gnome GUI). If you’re using KDE you want bluedevil (KDE GUI). If you want to do it from command line, skip the GUI.

No GUI

sudo apt install bluetooth rfkill bluez bluez-tools pulseaudio-module-bluetooth

GTK GUI

sudo apt install bluetooth rfkill blueman bluez bluez-tools pulseaudio-module-bluetooth

KDE GUI

sudo apt install bluetooth rfkill bluedevil bluez bluez-tools pulseaudio-module-bluetooth

Gnome GUI

sudo apt install bluetooth rfkill gnome-bluetooth bluez bluez-tools

You may want to reboot after installing these just to skip some headache if you aren’t really familiar with services on Linux. You can see how things are going with:

~$ systemctl status bluetooth
● bluetooth.service - Bluetooth service
   Loaded: loaded (/lib/systemd/system/bluetooth.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2019-10-12 01:50:51 CDT; 9h ago
     Docs: man:bluetoothd(8)
 Main PID: 525 (bluetoothd)
   Status: "Running"
   Memory: 3.2M
   CGroup: /system.slice/bluetooth.service
           └─525 /usr/lib/bluetooth/bluetoothd

If you see something else, you may want to restart the Bluetooth service.

sudo systemctl restart bluetooth

Checking For Bluetooth

We installed rfkill to be able to check our radio devices. If you run rfkill as root, you should get something like follows:

~$ sudo rfkill 
ID TYPE      DEVICE      SOFT      HARD
 0 wlan      phy0   unblocked unblocked
 1 bluetooth hci0   unblocked unblocked

If you see anything blocked, you will have to unblock it. Hard blocked is usually a switch (or a wireless key on the keyboard), soft blocked is usually something with the OS. Run the following to unblock a device:

rfkill unblock [device]

For my system, that would work out to:

rfkill unblock hci0

Turning Bluetooth On

If you are running Gnome or KDE, you probably don’t have to jump through all of these hoops and can just skip to using the GUI. Just start the specific application you installed and it should walk you through most of the process. My headset didn’t play nice with either blueman or bluedevil so I ended up turning to CLI. In order to turn on Bluetooth, we’re going to use bluetoothctl from command line first.

~$ bluetoothctl 
Agent registered
[bluetooth]# power on
Changing power on succeeded

Once you run bluetoothectl, you then need to run power on which will toggle the Bluetooth power on. You may have to do this power on process even if you are using a GUI.


