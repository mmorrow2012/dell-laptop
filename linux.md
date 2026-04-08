To repair GRUB on Ubuntu, the usual fix is to boot a live Ubuntu USB, mount your installed system, chroot into it, then reinstall GRUB and regenerate the menu. [cyberpanel](https://cyberpanel.net/blog/install-grub-on-ubuntu)

## Standard repair steps

1. Boot from an Ubuntu live USB and choose **Try Ubuntu**. [help.ubuntu](https://help.ubuntu.com/community/Boot-Repair)
2. Identify your Linux partitions with `lsblk` or `fdisk -l`. [youtube](https://www.youtube.com/watch?v=ZhxBJ3yh2OY)
3. Mount your root partition to `/mnt`. [cyberpanel](https://cyberpanel.net/blog/install-grub-on-ubuntu)
4. If you have a separate `/boot` or `/boot/efi`, mount those too. [youtube](https://www.youtube.com/watch?v=ZhxBJ3yh2OY)
5. Bind the system directories and chroot into your install. [cyberpanel](https://cyberpanel.net/blog/install-grub-on-ubuntu)
6. Reinstall GRUB:
   - BIOS/legacy: `grub-install /dev/sdX` [help.ubuntu](https://help.ubuntu.com/community/Grub2/Installing)
   - UEFI: `grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=ubuntu` [cyberpanel](https://cyberpanel.net/blog/install-grub-on-ubuntu)
7. Run `update-grub`, then exit and reboot. [help.ubuntu](https://help.ubuntu.com/community/Grub2/Installing)

## Example commands

For a typical UEFI system, the flow looks like this:

```bash
sudo mount /dev/sdXY /mnt
sudo mount /dev/sdXZ /mnt/boot/efi
sudo mount --bind /dev /mnt/dev
sudo mount --bind /proc /mnt/proc
sudo mount --bind /sys /mnt/sys
sudo chroot /mnt
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=ubuntu
update-grub
exit
sudo reboot
```

The exact device names matter, so replace `/dev/sdXY` and `/dev/sdXZ` with your real root and EFI partitions. [youtube](https://www.youtube.com/watch?v=ZhxBJ3yh2OY)

## Easier option

If you want a simpler method, Ubuntu’s Boot-Repair tool can often reinstall GRUB automatically from a live session. That is often the quickest route when you are not sure which partition or boot mode you are using. [linuxconfig](https://linuxconfig.org/ubuntu-22-04-boot-repair-how-to-guide)

## Common case checks

- If you see `grub rescue>`, the bootloader is damaged or cannot find its files. [geeksforgeeks](https://www.geeksforgeeks.org/techtips/how-to-fix-grub-bootloader-in-ubuntu/)
- If you dual-boot with Windows, make sure the EFI System Partition is mounted correctly and Windows Fast Startup is disabled if it is causing NTFS hibernation issues. [discourse.ubuntu](https://discourse.ubuntu.com/t/bootloader-grub-lost-after-ubuntu-upgrade-on-dual-boot/62345)
- If the GRUB menu is hidden but Ubuntu still boots, editing `/etc/default/grub` and setting a visible timeout can help. [cyberpanel](https://cyberpanel.net/blog/install-grub-on-ubuntu)

If you want, I can give you the exact commands for either **UEFI** or **legacy BIOS**, or for a **dual-boot Windows + Ubuntu** setup.

---

```bash
ubuntu@ubuntu:~$ lsblk
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop0         7:0    0   2.7G  1 loop /rofs
loop1         7:1    0     4K  1 loop /snap/bare/5
loop2         7:2    0  74.3M  1 loop /snap/core22/1612
loop3         7:3    0 271.2M  1 loop /snap/firefox/4848
loop4         7:4    0  91.7M  1 loop /snap/gtk-common-themes/1535
loop5         7:5    0  12.9M  1 loop /snap/snap-store/1113
loop6         7:6    0 505.1M  1 loop /snap/gnome-42-2204/176
loop7         7:7    0  38.8M  1 loop /snap/snapd/21759
loop8         7:8    0   500K  1 loop /snap/snapd-desktop-integration/178
sda           8:0    1  28.8G  0 disk 
└─sda1        8:1    1  28.8G  0 part /cdrom
nvme0n1     259:0    0 476.9G  0 disk 
├─nvme0n1p1 259:1    0   500M  0 part 
├─nvme0n1p2 259:2    0   128M  0 part 
├─nvme0n1p3 259:3    0 286.8G  0 part 
├─nvme0n1p4 259:4    0   4.8G  0 part 
├─nvme0n1p5 259:5    0  49.4G  0 part 
├─nvme0n1p6 259:6    0  90.7G  0 part 
├─nvme0n1p7 259:7    0   977M  0 part 
└─nvme0n1p8 259:8    0  43.7G  0 part 
ubuntu@ubuntu:~$ 
```
