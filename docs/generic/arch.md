# Quick Install

Source: [Official Install Guide](https://wiki.archlinux.org/index.php/Installation_guide)

## tl;dr

Skipping the obvious Download, Check image, Boot, Networking steps.

Simplest partition layout

UEFI Boot required GPT foo

/dev/sdX1 /mnt/boot 512MB Type:fat32
/dev/sdX2 /mnt      Rest of Disk Type:83
/dev/sdX3 [SWAP]    ~RAMsize Type:82

```
# If needed start ssh and obviously set root password
systemctl start sshd
# Set time
timedatectl set-ntp true
mkswap /dev/sdX3
swapon /dev/sdX3
mkfs.ext4 /dev/sdX2
# Select mirrors: /etc/pacman.d/mirrorlist
echo 'Server = http://mirrors.cat.net/archlinux/$repo/os/$arch' > /etc/pacman.d/mirrorlist
mkfs.fat -F32 /dev/sda1
mount /dev/sda2 /mnt
pacstrap /mnt base base-devel linux
genfstab -U -p /mnt >> /mnt/etc/fstab
arch-chroot /mnt
MYUSER=misp
MYHOSTNAME=misp-vm
ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
hwclock --systohc
LANG=C perl -i -pe 's/#(en_US.UTF)/$1/' /etc/locale.gen
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf
echo $MYHOSTNAME > /etc/hostname
# /etc/hosts
passwd
useradd -m -G wheel -s /bin/bash $MYUSER
passwd $MYUSER
pacman -Syy etckeeper
pacman -S sudo binutils
perl -i -pe 's/# (%wheel ALL=\(ALL\) ALL)/$1/' /etc/sudoers
pacman -S openssh dhcpcd grub efibootmgr dosfstools os-prober mtools
mkdir /boot/EFI
mount /dev/sda1 /boot/EFI
grub-install /dev/sda --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg
mkdir /boot/EFI/EFI/BOOT
##cp /boot/EFI/EFI/GRUB/grubx64.efi /boot/EFI/EFI/BOOT/BOOTX64.EFI
##echo 'bcf boot add 1 fs0:\EFI\GRUB\grubx64.efi "GRUB bootloader"
##exit' > /boot/EFI/startup.nsh
#echo "[Match]
#name=en*
#[Network]
#DHCP=yes" > /etc/systemd/network/enp0s3.network
systemctl enable dhcpcd
systemctl enable sshd
exit
umount /mnt/boot/EFI
umount /mnt
sync
reboot
```
