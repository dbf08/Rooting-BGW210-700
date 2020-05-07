# Rooting-BGW210-700
Rooting BGW210-700
 
Barring any network exploit being published and/or subsequently patched, here's a guide to physically root and extract certs from the demon spawn aka BGW210 without desoldering any flash chips and ruining the unit:

Needed equipment:
v1.5.12 or earlier RG firmware bin file
USB to TTL serial converter
Small screwdrivers (Phillips and flat head)
Razor blade (optional)
Soldering iron & decent soldering skills
USB extension cord (optional, but highly recommended)
FAT32 formatted USB thumbdrive
PuTTY
mfg_dat_decode utility from devicelocksmith.com
Flashing:
Downgrade RG to v1.5.12 (or earlier) firmware using RG web console. I used this file:
gateway.c01.sbcglobal.net/firmware/001E46/BGW210-700_1.6.9/spTurquoise210-700_1.6.9.bin

Disassembly & Setup:
Crack open the RG. It has three screws, two are under rubber feet, the last is under the yellow label. A razor blade will make quick work of it.
Pop off the front clear plastic covering the status lights.
Use a flat head screwdriver to GENTLY push in the plastic tabs holding it together.
Gently pull out the board. No need to disconnect the wifi antennas.
Locate four headerless pins labeled "BCM". One of them is square shaped.
Starting from the square pad, they are: Ground, VCC (3V), TX, and RX.
Soldered your TTL to the BCM pins, plug in the USB TTL. Red LED should light up faintly on front panel of RG.
Plug in your thumbdrive to a USB port on the back of RG.
Unplug USB TTL.
Open PuTTY, set to Serial connection, then find the COM port # in Device Manager.
Don't hit connect just yet.
Copy the following string to your clipboard: ^e

Rooting:
Boot up the RG.
Red light will be brightly lit.
Plug in USB TTL for the device.
Hit Open in PuTTY.

You should see a stream of messages hit your console. As soon as you see something like the following, right click to paste ^e into the console.

scsi 1:0:0:0: Direct-Access     General  UDisk            5.00 PQ: 0 ANSI: 2
sd 1:0:0:0: [sda] 15728640 512-byte logical blocks: (8.05 GB/7.50 GiB)
sd 1:0:0:0: [sda] Write Protect is off
sd 1:0:0:0: [sda] Mode Sense: 0b 00 00 08
sd 1:0:0:0: [sda] No Caching mode page present
sd 1:0:0:0: [sda] Assuming drive cache: write through
sd 1:0:0:0: [sda] No Caching mode page present
sd 1:0:0:0: [sda] Assuming drive cache: write through
 sda:
sd 1:0:0:0: [sda] No Caching mode page present
sd 1:0:0:0: [sda] Assuming drive cache: write through
sd 1:0:0:0: [sda] Attached SCSI removable disk
Note: If you don't wait long enough, USB won't be initialized. If you wait for the RG to fully boot, you can't get root cshell.

If it worked, you should see:

SysRq: intr handled on CPU 1
SysRq : Terminate All Tasks

Please press Enter to activate this console.
starting pid 987, tty '': '-/bin/cshell'

The SDB isn't running, so here's a regular shell.
# 
You've got root. Yay!

Now comes the fun part.
mkdir /tmp/sda
mount /dev/sda /tmp/sda/
mount mtd:mfg -t jffs2 /mfg
cp /mfg/mfg.dat /tmp/sda/
cp /etc/rootcert/*.der /tmp/sda
ls /tmp/sda
Look for output similar to:

arris-si-rootca.der         frontierroot.der
arris-si-subca.der          mfg.dat
attroot2031.der             motroot.der
attsubca2021.der            motsubca.der
If you've got the files, then unmount your thumbdrive.
umount /tmp/sda/

Unplug power from the RG, disconnect/desolder the the USB TTL. Throw RG in the garbage.
Just kidding. You'll probably want to return it to AT&T eventually.

mfg_dat_decode is marvelous magic
Copy all files off your thumbdrive to mfg_dat_decode\WIN32 folder from devicelocksmith.com
Run the tool, it'll read your certs and mfg.dat, then spit out a tarball (EAP-TLS_8021x_serial-number.tar.gz) to use with wpa_supplicant.

Pat yourself on the back, you're awesome.

Credits
/u/jhax01 for the overview in /r/homelab
