Connect to the internet

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


Update system clock
----------------------------

  `timedatectl set-ntp true`


==
Partition disk
----------------------------

  fdisk /dev/[...]

----------------------------
Set partition type
----------------------------
  In fdisk, use `t` to change type of EFI partition. Set to type 1

----------------------------
Format partitions
----------------------------

```
  mkfs.ext4 /dev/[root]
  mkswap /dev/[swap]
  mkfs.fat -F 32 /dev/[EFI]
```

----------------------------
Mount the file systems
----------------------------

  mount /dev/root_partition /mnt
  mount /dev/efi_system_partition /mnt/boot
  swapon /dev/swap_partition

----------------------------
Install
----------------------------

  configure reflector
```
  /etc/xdg/reflector/reflector.conf

  --save /etc/pacman.d/mirrorlist
  --country "United States"
  --protocol https
  --latest 5
```

  Enable reflector

```
  systemctl enable reflector.service
```

  install basic packages

```
pacstrap /mnt base linux linux-firmware vim man-db man-pages sudo networkmanager



----------------------------
configure system
----------------------------

```
genfstab -U /mnt >> /mnt/etc/fstab
```

----------------------------
Chroot
----------------------------

`arch-chroot /mnt`


----------------------------
Network
----------------------------
`hostnamectl set-hostname myhostname`

/etc/hosts
127.0.0.1        localhost
::1              localhost
127.0.1.1        myhostname


----------------------------
Password
----------------------------
set root password with

`passwd`


----------------------------
GRUB
----------------------------
get needed packages:

  `pacman -S grub efibootmgr`

run grub install:

  `grub-install --target=x86_64-efi --efi-directory=[esp] --bootloader-id=GRUB`

configure GRUB

  `grub-mkconfig -o /boot/grub/grub.cfg`


----------------------------
Set up desktop (ignore if ricing)
----------------------------
Install display server:

  `pacman -S xorg`

Install GNOME:

  `pacman -S gnome gnome-extra`


----------------------------
Prep for launch
----------------------------
launch GNOME Display Manager when system starts:
  `systemctl enable gdm.service`

launch Network Manager when system starts:
  `systemctl enable NetworkManager.service`
