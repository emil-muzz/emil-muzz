# SteamOS/SteamDeck Tricks and Tips


### Installing SteamOS (holo) on a Desktop PC 

SteamOS 3 (Holo) can be installed on desktop PCs with AMD GPUs using the image available from [HoloISO](https://github.com/theVakhovskeIsTaken/holoiso). This is an entirely usable build that offers the same functionality present on the Steam Deck. 

For reference this is my build:
* Intel Core i3-9100F @ 3.60 GHz
* Corsair Vengeance LPX DDR4 @ 16 GB
* Samsung 980 PCIe 3.0 NVMe SSD @ 1TB
* AMD Radeon RX 6600 @ 8GB VRAM
* NetGear A6100 USB WiFi

&nbsp;

For this guide I am using HoloISO Update 4.0 (Dec 11, 2022)

1. Write the image to a USB drive as instructed.
2. Boot the USB drive and install to the desired drive.
3. If you have a dkms driver to install, do it before restarting (see below).

Nearly all user data is isolated to the /home partition, so it is possible to re-install without loosing much of anything. 
The HoloISO install script will detect if an existing /home partition exists and prompt if you would like to keep it or not.

&nbsp;

### Adding a WiFi driver (dkms)
In this case a Netgear A6100 USB WiFi adapter with [this driver](https://github.com/morrownr/8821cu-20210118).
But this should also apply to dkms drivers for other devices.

I had a spare wifi router I configured as a WiFi to Ethernet bridge to download the source file archive from github. 
This may also be possible with the source file archive on a second USB drive.

1. After completing the installation script, open Konsole
2. Run the following:

```
(1)(liveuser@holoiso ~)$ sudo arch-chroot /mnt
[root@holoiso /]# mkdir -p /home/deck/src/
[root@holoiso /]# cd /home/deck/src/
[root@holoiso src]# git clone https://github.com/morrownr/8821au-20210708.git
[root@holoiso src]# cd 8821au-20210708
[root@holoiso 8821au-20210708]# sudo ./install-driver.sh 
```
I have had issues with the install script stalling out. Stopping (ctrl-z) it, running it a second time seems to work.
```
cleaning build area...
'make' -j4 KVER=5.13.0-valve21.3-1-neptune KSRC=/lib/modules/5.13.0-valve21.3-1-neptune/build.......^Z
[1]+  Stopped                 sudo ./install-driver.sh
[root@holoiso 8821au-20210708]# sudo dkms remove rtl8821au/5.12.5.2
[root@holoiso 8821au-20210708]# sudo ./install-driver.sh 
```
3. Reboot!

&nbsp;

### Updating HoloISO

1. Once the new Holo installation is booted, walk through the OOBE, sign-in and connect to wifi.
2. It seems like the pacman.conf file is out of date or deliberately disables the standard repositories.
(This appears to be corrected after upgrading holoiso in #3)
```
sudo vim /etc/pacman.conf 

# Comment out all the repo entries except holoiso-stable and holoisostaging
[holoiso-stable]
Include = /etc/pacman.d/holo_mirrorlist
SigLevel = Never

[holostaging]
Include = /etc/pacman.d/holo_mirrorlist
SigLevel = Never
```
3. Update HoloISO
```
# Update repos and packages
(deck@holoiso ~)$ sudo pacman -Syyu
```
4. Reboot

&nbsp;


### HoloISO Hotfix

### Game Backup/Restore

### ProtonGE

### Bottles

### DVD Ripping

### VLC Hardware Decoding

### Game Command Lines
BioShock Remastered (2K Launcher Bypass) 
```
eval $( echo "%command%" | sed "s/2KLauncher\/LauncherPatcher.exe'/Build\/Final\/BioshockHD.exe'/" ) 
```
