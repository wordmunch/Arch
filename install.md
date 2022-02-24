*assumes iso*

1 Connect to the internet
----------------------------

Enter wireless control untility with `iwctl`

Scan for networks:
  `station wlan0 scan`

List networks:
  `station wlan0 get-networks`

Connect to network:
  `station wlan0 connect SSID`

Check network status:
  `station wlan0 show`

exit iwctl with `exit`


2 Update system clock
----------------------------

  `timedatectl set-ntp true`


3 Partition disk
----------------------------
Use `fdisk` to format disks

`fdisk -l` will list currect disks and show partitions

Use `fdisk` to interact with disk:
  `fdisk /dev/[...]`

Delete with `d`, create new with `n`, change type with `t`

Partition:
- 512M for EFI boot partition
- Make swap partition if desired
- Use remainder for root

----------------------------
4 Format partitions
----------------------------

Format filesystem as ext4.
Format swap with `mkswap`.
Format EFI as FAT32.

```
  mkfs.ext4 /dev/[root]
  mkswap /dev/[swap]
  mkfs.fat -F 32 /dev/[EFI]
```

----------------------------
5 Mount the file systems
----------------------------

  mount /dev/root_partition /mnt
  mount /dev/efi_system_partition /mnt/boot
  swapon /dev/swap_partition

----------------------------
6 Optimize mirrors
----------------------------

  configure reflector
  
edit `/etc/xdg/reflector/reflector.conf`:

```
  --save /etc/pacman.d/mirrorlist
  --country "United States"
  --protocol https
  --latest 5
```

  Enable reflector

  `systemctl enable reflector.service`

----------------------------
7 Install basic packages
----------------------------
This covered my basics:

  `pacstrap /mnt base linux linux-firmware vim man-db man-pages sudo networkmanager`

----------------------------
8 Create fstab file
----------------------------

  `genfstab -U /mnt >> /mnt/etc/fstab`

----------------------------
9 Enter root
----------------------------

  `arch-chroot /mnt`


----------------------------
10 Setup basic network info
----------------------------

Add hostname to hostname file:

  `hostnamectl set-hostname myhostname`

/etc/hosts
127.0.0.1        localhost
::1              localhost
127.0.1.1        myhostname


----------------------------
11 Create password
----------------------------
set root password with

  `passwd`


----------------------------
12 GRUB
----------------------------
get needed packages:

  `pacman -S grub efibootmgr`

run grub install:

  `grub-install --target=x86_64-efi --efi-directory=[esp] --bootloader-id=GRUB`

configure GRUB

  `grub-mkconfig -o /boot/grub/grub.cfg`


----------------------------
13 Set up desktop (ignore if ricing)
----------------------------
Install display server:

  `pacman -S xorg`

Install GNOME:

  `pacman -S gnome gnome-extra`


----------------------------
14 Prep for launch
----------------------------
launch GNOME Display Manager when system starts:
  `systemctl enable gdm.service`

launch Network Manager when system starts:
  `systemctl enable NetworkManager.service`

----------------------------
15 Reboot
----------------------------
Exit chroot with `exit`

reboot with `reboot`
