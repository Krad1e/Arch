lsblk

sgdisk -o -n 1:0:+10M -t 1:EF02 -n 2:0:+500M -t 2:EF00 -n 3:0:0 -t 3:8300 /dev/sda

mkfs.fat -F32 /dev/sda2  
mkfs.ext4 /dev/sda3

mkdir -p /mnt/usb  
mount /dev/sda3 /mnt/usb

mkdir /mnt/usb/boot  
mount /dev/sda2 /mnt/usb/boot

pacstrap /mnt/usb linux linux-firmware base vim

genfstab -U /mnt/usb > /mnt/usb/etc/fstab

arch-chroot /mnt/usb

ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime

hwclock --systohc

vim /etc/locale.gen -> ucomment en_US.UTF-8 UTF-8

locale-gen

echo LANG=localeline >  /etc/locale.conf

echo hostname > /etc/hostname

vim /etc/hosts ->  
127.0.0.1  localhost  
::1        localhost  
127.0.1.1  hostname.localdomain  hostname

passwd

pacman -S grub efibootmgr

pacman -S networkmanager network-manager-applet mtools dosfstools reflector git base-devel linux-headers bluez-utils cups xdg-utils xdg-user-dirs

vim /etc/mkinitcpio.conf

mkinitcpio -p linux

EDITOR=vim visudo ->   
uncomment %wheel line

grub-install --target=i386-pc --recheck /dev/sda  
grub-install --target=x86_64-efi --efi-directory /boot --recheck --removable

grub-mkconfig -o /boot/grub/grub.cfg

vim /etc/systemd/network/10-ethernet.network

systemctl enable systemd-networkd.service

pacman -S iwd  
systemctl enable iwd.service

vim /etc/systemd/network/20-wifi.network

systemctl enable systemd-resolved.service  
ln -sf /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

systemctl enable systemd-timesyncd.service

useradd -m user  
passwd user

groupadd wheel  
usermod -aG wheel user

pacman -S sudo

EDITOR=vim visudo /etc/sudoers.d/10-sudo ->  
%sudo ALL=(ALL) ALL

groupadd sudo  
usermod -aG sudo user

pacman -S polkit

-> Reboot VM, boot VM from USB drive  
-> Login to arch linux  
-> su root

pacman -S xf86-video-vesa xf86-video-ati xf86-video-intel xf86-video-amdgpu xf86-video-nouveau

pacman -S xorg sddm plasma konsole packagekit-qt5 chromium

systemctl enable sddm

exit

reboot
