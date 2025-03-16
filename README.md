# Arch Linux Installation Guide

## 1. Connect to Wi-Fi

Open the Wi-Fi management tool:
```sh
iwctl  # Launch the Wi-Fi management tool
```
List available network devices:
```sh
device list  # Show network devices
```
Scan for available Wi-Fi networks:
```sh
station wlan0 get-networks  # List available networks
```
Connect to a Wi-Fi network:
```sh
station wlan0 connect "network-name"  # Replace "network-name" with your Wi-Fi SSID
```
Check the network connection:
```sh
ping -c 5 archlinux.org  # If you receive responses, the connection is successful
```

---

## 2. Partition the Disk

List available storage devices:
```sh
lsblk  # Display storage devices and partitions
```
Open the partition manager:
```sh
cfdisk /dev/sda  # Replace /dev/sda with your target disk
```
### Partitioning Recommendations:
- **Linux System Partition** (`/` - root)
- **Swap Partition** (optional, useful for low RAM systems)
- **EFI Partition** (`/boot/efi`, required for UEFI, format as FAT32)

---

## 3. Format and Mount Partitions

Format the Linux system partition:
```sh
mkfs.ext4 /dev/sda1  # Format as ext4
```
Format the EFI partition (if applicable):
```sh
mkfs.fat -F32 /dev/sda2  # Format as FAT32
```
Set up the swap partition (if applicable):
```sh
mkswap /dev/sda3  # Create a swap partition
swapon /dev/sda3  # Enable swap
```
Mount the root partition:
```sh
mount /dev/sda1 /mnt  # Mount Linux system partition
```
Mount the EFI partition (if applicable):
```sh
mkdir -p /mnt/boot/efi
mount /dev/sda2 /mnt/boot/efi
```

---

## 4. Install the Base System

Install essential packages:
```sh
pacstrap /mnt base linux linux-firmware  # Install Arch Linux base system
```
Generate `fstab`:
```sh
genfstab -U /mnt >> /mnt/etc/fstab  # Create partition table with UUIDs
```
Enter the new system:
```sh
arch-chroot /mnt  # Switch to the new system
```

---

## 5. Configure the System

Set the time zone:
```sh
ln -sf /usr/share/zoneinfo/Asia/Ho_Chi_Minh /etc/localtime  # Set the timezone to Vietnam
hwclock --systohc  # Sync hardware clock
```
Configure locale:
```sh
echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen  # Enable English locale
echo "vi_VN.UTF-8 UTF-8" >> /etc/locale.gen  # Enable Vietnamese locale (optional)
locale-gen  # Generate locale
```
Set system language:
```sh
echo "LANG=en_US.UTF-8" > /etc/locale.conf  # Set system language to English
```
Set hostname:
```sh
echo "archlinux" > /etc/hostname  # Set the hostname
```
Configure network:
```sh
echo "127.0.0.1 localhost" >> /etc/hosts
echo "::1 localhost" >> /etc/hosts
echo "127.0.1.1 archlinux.localdomain archlinux" >> /etc/hosts
```
Set root password:
```sh
passwd  # Set the root password
```

---

## 6. Install the Bootloader (GRUB)

For UEFI systems:
```sh
pacman -S grub efibootmgr  # Install GRUB and EFI boot manager
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB  # Install GRUB
grub-mkconfig -o /boot/grub/grub.cfg  # Generate GRUB configuration
```
For BIOS (Legacy) systems:
```sh
pacman -S grub  # Install GRUB
grub-install --target=i386-pc /dev/sda  # Install GRUB for BIOS
grub-mkconfig -o /boot/grub/grub.cfg  # Generate GRUB configuration
```

---

## 7. Install Additional Packages (Optional)

You can install useful utilities:
```sh
pacman -S git nano make gcc rust less neofetch kitty  # Install optional tools
```

---

## 8. Connect to Wi-Fi After Installation

After rebooting, connect to Wi-Fi using:
```sh
nmcli dev wifi connect "wifi_name" password 'password'  # Connect to Wi-Fi
```
Or using `iwd`:
```sh
iwctl
station wlan0 connect "wifi_name"
```

---

## 9. Complete Installation

Exit chroot and reboot:
```sh
exit  # Exit chroot
umount -R /mnt  # Unmount all partitions
reboot  # Restart the system
```

Congratulations! 🎉 You have successfully installed Arch Linux! 🚀

