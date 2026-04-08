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
ubuntu@ubuntu:~$ blkid
/dev/nvme0n1p1: UUID="208C-B6FE" BLOCK_SIZE="512" TYPE="vfat" PARTLABEL="EFI system partition" PARTUUID="a4879aec-9141-4122-ae4e-64da12051ef1"
/dev/nvme0n1p3: LABEL="Windows" BLOCK_SIZE="512" UUID="CEF69891F6987C01" TYPE="ntfs" PARTLABEL="Basic data partition" PARTUUID="9de1208c-e10d-416c-a715-9233802aa727"
/dev/nvme0n1p4: LABEL="Recovery" BLOCK_SIZE="512" UUID="A6948EF4948EC5F1" TYPE="ntfs" PARTLABEL="Basic data partition" PARTUUID="a76f6955-079e-4360-9bae-61c2c3182f97"
/dev/nvme0n1p5: UUID="a2f06285-5e81-475a-a4bc-176aecada20b" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="fc397652-d97d-415d-ae18-3b0a9b213510"
/dev/nvme0n1p6: UUID="d0f7d013-d743-4263-85e6-81d5a5945492" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="3e39ac55-edcc-456c-8e09-fecfb62a6d58"
/dev/nvme0n1p8: UUID="3bb9183a-66a4-4fc5-9d9b-fb03e4e99797" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="1d870085-8f18-43a5-b0ed-035472eb27b3"
/dev/sda1: LABEL="UBUNTU22" UUID="A266-CC8A" BLOCK_SIZE="512" TYPE="vfat" PARTUUID="2bc0126f-01"
ubuntu@ubuntu:~$ 

```
---

Your disk layout looks like a typical UEFI setup, and the most likely Ubuntu EFI partition is **`nvme0n1p1`** because it is the 500 MB partition at the start of the NVMe drive. The Ubuntu root partition is likely one of the larger Linux partitions, but I would need its filesystem type or mountpoint to name it with confidence. [forum.ubuntu-fr](https://forum.ubuntu-fr.org/viewtopic.php?id=2083199)

## What to mount

Use these as the first guesses:

- EFI System Partition: `nvme0n1p1` [reddit](https://www.reddit.com/r/Gentoo/comments/1ikkaj6/i_did_grubinstall_devnvme0n1_instead_of/)
- Ubuntu root partition: likely one of `nvme0n1p3`, `nvme0n1p4`, `nvme0n1p5`, `nvme0n1p6`, `nvme0n1p7`, or `nvme0n1p8`, depending on where Ubuntu is installed [reddit](https://www.reddit.com/r/linuxquestions/comments/us10r5/repair_boot_brug/)

## Repair commands

If `nvme0n1p5` turns out to be your Ubuntu root partition, the repair flow would be:

```bash
sudo mount /dev/nvme0n1p5 /mnt
sudo mount /dev/nvme0n1p1 /mnt/boot/efi

for i in /dev /proc /sys /run; do sudo mount --rbind "$i" "/mnt$i"; done
sudo mount --bind /sys/firmware/efi/efivars /mnt/sys/firmware/efi/efivars

sudo chroot /mnt
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=ubuntu
update-grub
exit

sudo reboot
```

That `--efi-directory=/boot/efi` step is the standard UEFI reinstall method, and binding `efivars` helps GRUB and `efibootmgr` work correctly in the chroot. [wiki.debian](https://wiki.debian.org/GrubEFIReinstall)

## If Windows should appear

After `update-grub`, Ubuntu usually detects Windows Boot Manager again if it is still present on the EFI partition. If it does not appear, the usual causes are that the Windows EFI files are missing, the wrong partition was mounted, or Fast Startup/hibernation is interfering. [youtube](https://www.youtube.com/watch?v=HPHZ3hSp6mE)

## Safer next check

Before running the reinstall, identify the Ubuntu root partition by checking which one has `ext4`, `btrfs`, or `xfs` once mounted, or by using `blkid` output. The EFI partition should be FAT32 and usually small, which matches `nvme0n1p1` here. [ubuntu-mate](https://ubuntu-mate.community/t/system-restore-or-fast-migration-to-another-ssd-with-different-disk-structure/23906/16)

If you want, I can help you identify the exact Ubuntu root partition from the remaining `blkid` output and then give you the exact commands with the right device names.

---

<img width="593" height="97" alt="image" src="https://github.com/user-attachments/assets/08d84986-46d6-4538-8208-0e8a4d9bfea3" />

<img width="925" height="597" alt="image" src="https://github.com/user-attachments/assets/5fee25b4-69bb-4884-98d9-cf6ff37fe414" />

<img width="928" height="650" alt="image" src="https://github.com/user-attachments/assets/4b51e047-e291-4542-b3ce-03a2546cabdc" />

---

That error means you are running `update-grub` from the live session instead of from inside your installed system, so GRUB is trying to probe the live environment’s `/cow` overlay. You must chroot into your installed Ubuntu first, then run `update-grub` there. [wiki.debian](https://wiki.debian.org/GrubEFIReinstall)

## Do this in order

From your current live session terminal:

```bash
# 1. Mount your Ubuntu root partition (adjust if yours is different)
sudo mount /dev/nvme0n1p5 /mnt

# 2. Mount the EFI partition
sudo mount /dev/nvme0n1p1 /mnt/boot/efi

# 3. Bind-mount system directories
for i in /dev /proc /sys /run; do sudo mount --rbind "$i" "/mnt$i"; done
sudo mount --bind /sys/firmware/efi/efivars /mnt/sys/firmware/efi/efivars

# 4. Chroot into the installed system
sudo chroot /mnt

# 5. Now run update-grub from inside the chroot
update-grub

# 6. Reinstall GRUB to the EFI partition
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=ubuntu

# 7. Exit and reboot
exit
sudo reboot
```

Key points:

- The `/cow` error happens because the live session uses a copy-on-write overlay; `update-grub` must be run from the real root filesystem in a chroot. [reddit](https://www.reddit.com/r/linuxquestions/comments/us10r5/repair_boot_brug/)
- Make sure `/dev/nvme0n1p5` is actually your Ubuntu root partition. If you are unsure, confirm with:
  ```bash
  sudo blkid | grep -E 'ext4|btrfs|xfs'
  ```
  and match the UUID to the one in `/etc/fstab` once mounted. [ubuntu-mate](https://ubuntu-mate.community/t/system-restore-or-fast-migration-to-another-ssd-with-different-disk-structure/23906/16)

---


Yes — that **is** a third Linux install, and it’s already the active system you’re booted into on `/dev/nvme0n1p5`. `update-grub` found the other two installs because `os-prober` is working, but your boot menu is still hidden, so you may not see all entries unless you change the GRUB timeout settings. [help.ubuntu](https://help.ubuntu.com/community/Grub2/Setup)

## What your output means

- `/dev/nvme0n1p5` is the current root filesystem, so it is your installed Ubuntu system. [baeldung](https://www.baeldung.com/linux/grub-bootloader-add-new-os)
- `/dev/nvme0n1p6` and `/dev/nvme0n1p8` are separate Linux installs that GRUB detected automatically. [omgubuntu.co](https://www.omgubuntu.co.uk/2021/12/grub-doesnt-detect-windows-linux-distros-fix)
- If you expected a *different* third Linux system, it may already be the one you’re booted into, or it may not be scanned because it is not installed as a normal bootable Linux system. [umutsagir](https://www.umutsagir.com/solution-to-os-prober-not-finding-other-operating-systems-windows-linux/)

## Why it looks confusing

GRUB does not list “everything on disk” in a simple way. It adds bootable entries it can detect, and on Ubuntu that usually means the current system, other Linux installs found by `os-prober`, and Windows boot entries. Since your menu is hidden with `GRUB_TIMEOUT_STYLE=hidden` and `GRUB_TIMEOUT=0`, the entries can exist but remain invisible at startup. [help.ubuntu](https://help.ubuntu.com/community/Grub2/Setup)

## What to do next

Set the menu to show briefly so you can confirm all entries are present:

```bash
GRUB_TIMEOUT_STYLE=menu
GRUB_TIMEOUT=5
```

Then run:

```bash
sudo update-grub
```

This should let you see whether the third install appears in the menu. [omgubuntu.co](https://www.omgubuntu.co.uk/2021/12/grub-doesnt-detect-windows-linux-distros-fix)

## If the third OS still does not appear

If it is a separate Linux distribution, you may need a manual GRUB entry or a `custom.cfg` entry that chainloads the other system’s own GRUB configuration. That is often the cleanest approach when `os-prober` detects some systems but misses one partition. [forum.endeavouros](https://forum.endeavouros.com/t/how-to-create-custom-grub-menu-entry-for-other-os/53423)

---

**Grub Customizer** is the easiest way to rename those entries visually without editing generated files. Here's how:
## Install Grub Customizer (Recommended)
```bash
sudo add-apt-repository ppa:danielrichter2007/grub-customizer
sudo apt update
sudo apt install grub-customizer
```

Launch it (`grub-customizer`), authenticate as root, and you'll see your boot entries listed. Just double-click each Ubuntu 24.04 entry and rename:
- Current system → "Ubuntu 24.04.4 - instance #1"  
- `/dev/nvme0n1p8` → "Ubuntu 24.04.4 - instance #2"

Click **Save**. [youtube](https://www.youtube.com/watch?v=IRxC-WEhjkI)
## Manual method (if you prefer CLI)
Create `/etc/grub.d/40_custom` and add:

```
menuentry 'Ubuntu 24.04.4 - instance #1' --class ubuntu --class gnu-linux --class gnu --class os {
	set root='hd0,gpt5'
	linux /vmlinuz-6.14.0-24-generic root=UUID=<uuid-of-p5> ro quiet splash
	initrd /initrd.img-6.14.0-24-generic
}

menuentry 'Ubuntu 24.04.4 - instance #2' --class ubuntu --class gnu-linux --class gnu --class os {
	set root='hd0,gpt8'
	linux /vmlinuz root=UUID=<uuid-of-p8> ro quiet splash
	initrd /initrd.img
}
```

**Get UUIDs first:**
```bash
sudo blkid /dev/nvme0n1p5
sudo blkid /dev/nvme0n1p8
```

Then `sudo update-grub`.
## Recommendation
Use **Grub Customizer** — it's purpose-built for exactly this and survives `update-grub` runs. Your multi-boot setup is already perfect; this just gives clearer labels. [ubuntuhandbook](https://ubuntuhandbook.org/index.php/2022/04/install-grub-customizer-ubuntu-2204/)

Reboot after saving to test!
