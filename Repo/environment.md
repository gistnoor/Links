#### Python   
###### Scrapy
--------------
sudo apt-get install python3-pip python3-lxml python3-crypto python3-cssselect python3-openssl python3-w3lib python3-twisted python3-dev libxml2-dev libxslt1-dev zlib1g-dev libffi-dev libssl-dev


## Operation System
================================
###Arch Linux
----------------------------
###### Installation 

☯ Check the internet connection
```bash
ping -c 3 www.google.com
```

**☯ If you are out of luck then configure your ethernet connection**

Check that your kernel is compiled with pppoe support
```bash
zgrep CONFI_PPPOE /proc/config.gz
```

output ``CONFIG_PPPOE=m``

**☯ create connection configuration**
At first check your ethernet driver
```bash
ip link
```
or
```bash
ip addr
```

Create the connection configuration file: ``/etc/ppp/peers/your_provider``
```bash
plugin rp-pppoe.so
# rp_pppoe_ac 'your account name'
# rp_pppoe_service 'your service name'
 
# network interface
``enp3s0f1``
# login name
name ``"net74"``
usepeerdns
persist
# Uncomment this if you want to enable dial on demand
#demand
#idle 180
defaultroute
hide-password
noauth
```

………….Put a line like this in ``/etc/ppp/pap-secrets`` or ``/etc/ppp/chap-secrets``
```bash
``net74`` * ``7474n``
```

**☯You can now start the link using the command:**
```bash
pppd call ``net74``
```
or
```bash
pon ``net74``
```	

**☯ Check if efi is activate**
```bash
efivar -l
```

**☯ Check Hard drive**
```bash
lsblk
```	

**☯ Wipe the hard disk and get ready for partition**
```bash
gdisk /dev/sda
```
```bash
comman (? for help): x
```	

```bash
Expert command (? for help): z
```	

```bash
Blak out MBR (Y/N): y
```

**☯ Then creating partition**
```bash
cgdisk
```	

```bash
/dev/sda
```	

**☯ Partition Configuration**
```bash
boot partition
Size in Sectors …. -> 300MiB
HEX code …………-> EF00
name …………….-> boot
```
```bash
Swap partition
Size in Sectors ……-> ram size * 1.5
HEX Code…………..-> 8200
name ……………..-> swap
```
```bash
root partition
Size in sectors …….-> !!!
Hex Code …………..->
name ………………-> root
```
```bash
write -> quit
```

Reboot the computer to make sure the new partition takes part in installation process.

**☯ Configure partition types**

```bash
mkfs.fat -F32 /dev/sda1
```

```bash
mkswap /dev/sda2
```	

```bash
swapon /dev/sda2
```	

```bash
mkfs.ext4 /dev/sda3
```	

```bash
mkfs.ext4 /dev/sda4
```	

(Optional -> in case home is separated from root)

**☯ Start Installation**
```bash
mkdir /mnt
```	

```bash
mount /dev/sda3 /mnt
```	

```bash
mkdir /mnt/boot
```	

```bash
mount /dev/sda1 /mnt/boot
```	

**☯ Copy the mirrorlist and sort out the best server for you (It takse 5 to 10 minutes)**

```bash
cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup
```	

```bash
sed -i 's/^@Server/Server/' /etc/pacman.d/mirrorlist.backup
```	

```bash
rankmirrors -n 6 /etc/pacman.d/mirrorlist.backup > /etc/pacman.d/mirrorlist
```	

**☯ Install the base package**

```bash
pacstrap -i /mnt base base-devel
```	

**☯ Genarate fstab file**

```bash
genfstab -U -p /mnt >> /mnt/etc/fstab
```	

verify your fstab

```bash
nano /mnt/etc/fstab
```	

**☯ Chroot into your installed arch linux**

```bash
arch-chroot /mnt
```	

**☯ Set up your language**

```bash
nano /etc/locale.gen
```	

………From the list uncomment

```bash
"en_US.UTF-8"
```	

**☯ Genarate locale gen**

```bash
locale-gen
```	

**☯ Language Setup**

```bash
echo LANG=en_US.UTF-8 > /etc/locale.conf
```	

```bash
export LANG=en_US.UTF-8
```	

**☯ Set Time**

```bash
ls /usr/share/zoneinfo/
```	

```bash
ln -s /usr/share/zoneinfo/Asia/Dhaka > /etc/localtime
```	

```bash
hwclock --systohc --utc
```	

**☯ Set up hostname**

```bash
echo noor > /etc/hostname
```	
```bash
nano /etc/hosts
``write_your_hostname``
```

**☯ List of packages**

```bash
nano /etc/pacman.conf
```

``………….Uncomment the following lines``
```bash
[multilib]
include = /etc/pacman.d/mirrorlist
```

``…………. and the end of file append the following lines``
```bash
[archlinuxfr]
SigLevel = Never
Server = http://repo.archlinux.fr/$arch
```

**☯ Update the system**

```bash
pacman -Syu
```

**☯ Install yaourt**

```bash
pacman -S yaourt
```

**☯ password for root**

```bash
passwd
```

**☯ Create default user and password**
```bash
useradd -m -g users -G wheel,storage,power -s /bin/bash asif
passwd asif
```

**☯ Edit sudoers to unable use of sudo**

```bash
EDITOR=nano visudo
```	

………………….uncomment the follwing line
Uncomment to allow member of group wheel to execute any command

```bash
%wheel ALL=(ALL) ALL
```

…………………….. And add a line after this line

```bash
Defaults rootpw
```

**☯ Install bash completion**

```bash
pacman -S bash-completion
```

**☯ check efi variables are mounted**

```bash
mount -t efivarfs efivarfs /sys/firmware/efi/efivars
```

**☯ Install bootloader**
```bash
pacman -S systemd
bootctl install
```

**☯ Configure microcode**

```bash
sudo pacman -S linux-firmware intel-ucode
```

**☯ Entry files**

```bash
nano /boot/loader/entries/arch.conf

```
………Add the following lines

```bash
title   Arch Linux
linux   /vmlinuz-linux
initrd  /intel-ucode.img
initrd  /initramfs-linux.img
options root=/dev/sda3 rw
```

**☯ For wifi connection download**

```bash
sudo pacman -S iw wpa_supplicant dialog actiond wpa_actiond
yaourt -S b43-firmware broadcom-wl-dkms
```

……………… sometimes it requires
```bash
pacman -S linux-headers
```
………………modprobe (wl)

**☯ Exit and reboot**

```bash
exit
umount -R /mnt
```

**☯ For wifi connection**

```bash
sudo ip link set wl2pss0 down
sudo wifi-menu
```

………………..check
```bash
ping -c 3 http://www.google.com
```

**☯ Install xorg**

```bash
sudo pacman -S xorg-server xorg-server-utils xorg-xinit xorg-twm xorg-xclock xterm
```

**☯ Install your preffered display environment**
``For Gnome``

```bash
sudo pacman -S gnome gnome-extra
```

``For mate``

```bash
sudo pacman -S mate mate-extra
```

``For i3``
```bash
sudo pacman -S i3
```

**☯ Install display manager**
```bash
gnome has default gdm
```

For others: ``lightdm`` can be used

```bash
sudo pacman -S lightdm ligthdm-gtk-greeter lightdm-gtk-greeter-settings
```

**☯ Configure lightdm**

```bash
sudo nano /etc/lightdm/lightdm.conf
```

………………..change the following file
```bash
[Seat:*]
…
greeter-session=default-greeter
```
to

```bash
greeter-session=lightdm-gtk-greete
```	



☯ Enable lightdm

```bash
sudo systemctl enable lightdm.service
```

**☯ Configure xinit**
```bash
cp /etc/X11/xinit/xinitrc ~/.xinitrc
```
 
```bash
nano ~/.xinitrc
```

…………………-> append

```bash
xscreensaver &
xsetroot -cursor_name left_ptr &
exec i3/gnome-session/xfce4session/mate-session
```

**☯ Autostart x at login**
……………..append this line to the bottom of the ~/.bash_profile
 
```bash
[[ -z $DISPLAY && $XDG_VTNR -eq 1 ]] && exec startx
```
