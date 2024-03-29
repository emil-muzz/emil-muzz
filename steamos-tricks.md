# SteamOS/SteamDeck Tricks and Tips


### Installing SteamOS (holo) on a Desktop PC 

SteamOS 3 (Holo) can be installed on desktop PCs with AMD GPUs using the image available from [HoloISO](https://github.com/theVakhovskeIsTaken/holoiso). This is an entirely usable build that offers the same functionality present on the Steam Deck. 

For reference this is my build:
* Intel Core i3-9100F @ 3.60 GHz
* Corsair Vengeance LPX DDR4 @ 16 GB
* Samsung 980 PCIe 3.0 NVMe SSD @ 1TB
* AMD Radeon RX 6600 @ 8GB VRAM
* ~~NetGear A6100 USB WiFi~~

&nbsp;

~~For this guide I am using HoloISO Update 4.0 (Dec 11, 2022)~~

:warning: [HoloISO Immutable](https://github.com/holoiso-staging/releases) is now available, but makes some **significant** changes to the system that **breaks** some of the functionality below.

1. Write the image to a USB drive as instructed.
2. Boot the USB drive and install to the desired drive.
3. If you have a dkms driver to install, do it before restarting (see below).

Nearly all user data is isolated to the /home partition, so it is possible to re-install without loosing much of anything. 
The HoloISO install script will detect if an existing /home partition exists and prompt if you would like to keep it or not.

### Adding a WiFi driver (dkms)

:warning: **(simple) DKMS IS BROKEN IN HOLOISO IMMUTABLE** :warning: 

_I'm sure it's possible with some filesystem overlays and a bit of hackery, but I needed a working system, had a workaround, and didn't have the time to figure out how to do it properly._
_The onboard ethernet **is** supported by the kernel, your mileage may vary. The workaround was to use the spare router as an ethernet to wifi adapter instead of the A6100 USB. Is it efficient? No. Is it convenient? Also No. But it works._

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

:warning: _TODO: Update for HoloISO Immutable. Steam updates apply like they do on the Steam Deck. Discover can still update (some) packages. Will have to wait for another HoloISO release to see what the upgrade path is like._

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

### Sound Stutter Fix

:warning: _TODO: Update for HoloISO Immutable. I haven't noticed this re-occurring, the only audio glitch I've experienced is an inital delay when audio starts playing verses when it's audible from the speakers. This may have something to do with HDMI or my built-in speakers._

A solution to the audio cutting out, although I cannot find the source now.
'deck' is the user running pipewire (ps axu | grep pipewire)
```
sudo pacman -S realtime-privileges rtkit
sudo systemctl enable --now rtkit-daemon
sudo gpasswd -a deck rtkit 
sudo gpasswd -a deck realtime
reboot
```


&nbsp;

### Swap Space / CryoByte Utilities

:warning: _TODO: Update for HoloISO Immutable. I didn't have any trouble running CyroByte Utilities, applying recommended settings, or enabling swap after installing immutable._

I used the [ArchWiki documentation](https://wiki.archlinux.org/title/Swap) to create and mount a 16G swapfile (/home/deck/.swapfile), then used [CryoByte33's Steam Deck Utilties](https://github.com/CryoByte33/steam-deck-utilities) scripts to set swappiness to 1, enabled Huge Pages, Shared Memory in THP, Compation Proactiveness, Huge Page Defragmentation, and Page Lock Unfairness. 

The storage scripts can also be very helpful in nudging up performance and freeing up storage space. 

&nbsp;

### Game Backup/Restore

Everyone has their own approach to library management. I previously (on Windows) used Steam's backup/restore function and stored backups on removable hard drives (WD Passport). I ran into problems restoring backups made in Windows to my holo install, but that may no longer be an issue. (Things in this area are improving, largely due to the popularity of the Steam Deck. ~~Steam's backup/restore feature hasn't really evolved since it's release.~~) _Improvement has been made on Steam's backup functionality (goodbye fixed CD/DVD sizes) and being able to pull from other Steam libraries over local wifi is very helpful._

I still use the removable hard drives as additional steam library folders, similar to SD cards on the Steamn Deck. It does make keeping games updated easier, at the cost of no compression. I still manually copy game folders from the removable drive to the library folder on my SSD, then (with the drive/library removed) install the game to the SSD and let Steam discover the existing game files. 

&nbsp;

### ProtonGE

:warning: _ProtonUp-QT and ProtonGE still work fine in immutable._

Get ProtonUp-QT from Discover to install it. 
Used it in combination with Heroic to get the EGS version of XCom2 running (see below).

&nbsp;

### Bottles 

:warning: _TODO: Update for HoloISO Immutable. I haven't had a need to test this in immutable yet, but it should work fine._

Wine frontend that can be useful for running windows apps (Doxie scanner software tested ok.)

&nbsp;

### DVD Ripping
:warning: _TODO: Update for HoloISO Immutable. This might be broken with immutable since it depends on local packages. It should be possible to install the packages and run these steps from a chroot jail._

I am still moving a sizable DVD library onto a NAS for playback over Roku media player. (More on that later.) For DVD ripping I use some fairly common tools and encode to HandBrake's official Matroska H.265 MKV 480p30 profile. You'll need to install the following with pacman: 
* dvdbackup
* libdvdcss
* cdrtools

&nbsp;

#### Read DVD 
```
dvdbackup -i /dev/sr0 -I
```
#### Rip DVD
```
dvdbackup -i /dev/sr0 -o /home/deck/dvd/ -M
```
#### Create ISO
```
mkisofs -dvd-video -udf -o /home/deck/dvd/moviename.iso /home/deck/dvd/moviename
```
### Open the ISO with HandBrake and encode!

(I have also had luck with the flatpak version of MakeMKV, then re-encode to H.265 @ 480p)

&nbsp;

### VLC Hardware Decoding
VLC (FlatPak) had trouble with playing back mkv files correctly out of box. Changing the hardware accelerated decoding method to VDPAU video decoder solved this problem for me. 

### Audio Video Library Management
Movies, TV Shows, and Music are stored on a standalone NAS and shared across the local LAN.  
The NAS is a DIY project made possible by OpenMediaVault, RockPi4, 4x SATA board, and ~8 TB of HDDs.
(ToDo: Document the NAS someday.)

To manage video naming, covers, and information (all displayed on Roku media player natively) I am using [tinyMediaManager](https://www.tinymediamanager.org/). It will complain about missing libmediainfo, you can install it via pacman.

&nbsp;

### Mounting network (smb) shares

:warning: _TODO: Update for HoloISO Immutable. This is likely broken with immutable.

Again pointing to the [ArchWiki documentation](https://wiki.archlinux.org/title/Samba) on samba. 

Setup file based credentials (owned by root)
```
sudo mkdir -p /etc/samba/credentials/
```
Contents of /etc/samba/credentials/share:
```
username=myuser
password=mypass
```
The credential file should explicitly readable/writeable to root:
```
sudo chown root:root /etc/samba/credentials
sudo chmod 700 /etc/samba/credentials
sudo chmod 600 /etc/samba/credentials/share
```
And replace username=myuser,password=mypass with credentials=/etc/samba/credentials/share in the mount command.
```
sudo mount --mkdir -t cifs //server/share /run/media/deck/share -o credentials=/etc/samba/credentials/share,workgroup=workgroup,iocharset=utf8,uid=username,gid=group
```

&nbsp;

### Game Command Lines
BioShock Remastered (Launcher Bypass)
```
eval $( echo "%command%" | sed "s/2KLauncher\/LauncherPatcher.exe'/Build\/Final\/BioshockHD.exe'/" ) 
```
BioShock 2 Remastered (Launcher Bypass)
```
eval $( echo "%command%" | sed "s/2KLauncher\/LauncherPatcher.exe'/Build\/Final\/Bioshock2HD.exe'/" )
```
BioShock Infinte (Launcher Bypass)
```
eval $( echo "%command%" | sed "s/2KLauncher\/LauncherPatcher.exe'/Binaries\/Win32\/BioShockInfinite.exe'/" )
```
Cyberpunk 2077 (Launcher Bypass, Skip Start Screen)
```
--launcher-skip -skipStartScreen
```
Dishonored 2  (Skip Intro Movies)
```
+com_showLoadingScreen 0
```
Fallout 4 (Launcher Bypass)
```
eval $( echo "%command%" | sed "s/Fallout4Launcher.exe'/Fallout4.exe'/" )
```
Fallout New Vegas (NVSE Launcher)
(ToDo: A guide on how I got TTW working.)
```
eval $( echo "%command%" | sed "s/FalloutNVLauncher.exe'/nvse_loader.exe'/" )
```

### Epic Games Store
XCOM 2 - Using Heroic, GE-Proton7-54, 
Skip laucher with alternate EXE to run: 
```
/home/deck/Games/Heroic/XCOM2/Binaries/Win64/XCom2.exe
```
To fix not being able to move characters, use Heroic's 'Run EXE on Prefix' and execute /home/deck/Games/Heroic/XCOM2/Binaries/PreReqRedist/vcredist_x64.exe

