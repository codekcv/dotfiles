# dotfiles

This is a personal guide for my own arch install for my reference only.

// ===[ Preparation ]=== //
1. Download Arch ISO & Balena Etcher
2. Flash to drive and boot to UEFI

// ===[ Installation ]=== //
1. Check stuff
1.1 internet >'ping -c3 google.com'
1.2 UEFI >ls /sys/firmware/efi/efivars
1.3 disk >fdisk -l

2. timedatectl set-ntp true

5. Wipe
5.1 gdisk /dev/sd*
5.2 o -> n -> def, def, +512MB, ef00 -> n -> def def def -> w

6. Format
6.1 mkfs.fat /dev/sd*1
6.2 mkfs.ext4 /dev/sd*2

7. Mount
7.1 mount /dev/sd*2 /mnt
7.2 mkdir /mnt/boot/efi
7.3 mount /dev/sd*1 /mnt/boot/efi

// ===[ INIT PACKAGES ]=== //
pacstrap /mnt base base-devel linux linux-firmware grub efibootmgr networkmanager man amd-ucode xf86-video-amdgpu xorg xorg-xinit xorg-xrdb i3-gaps i3blocks rofi rxvt-unicode nano git udisks2 ntfs-3g p7zip zsh picom alsa-utils pulseaudio-alsa pavucontrol nitrogen neofetch vlc nautilus eog code qbittorrent gimp libreoffice-still

// ===[ FSTAB ]=== //
genfstab -U /mnt >> /mnt/etc/fstab

// === ARCH-ACHROOT
arch-chroot /mnt /bin/bash

>systemctl enable NetworkManager
>grub-install /dev/sd*
>grub-mkconfig -o /boot/grub/grub.cfg

// ===[ TIMEZONE ]=== //
ln -sf /usr/share/zoneinfo/Singapore /etc/localtime
hwclock --systohc

// ===[ Locale ]=== //
1. >nano /etc/locale.gen
2. uncomment english example = en_US.UTF-8 UTF-8
3. >locale-gen
4. >nano /etc/locale.conf
5. Type "LANG=en_US.UTF-8

// ===[ HOSTNAME ]=== //
1. >nano /etc/hostname -> arch
2. >nano /etc/hosts
127.0.0.1 localhost
::1 localhost
127.0.1.1 arch.localdomain arch

// ===[ ACCOUNTS ]=== //
1. Set root ps. >password

>useradd -m -G wheel [name]
>passwd [name]
>nano /etc/sudoers -> uncomment wheel all

>exit
>umount -R /mnt
>reboot

// ON ACCOUNT!
// ===[ AUR HELPER ]=== //
>sudo git clone https://aur.archlinux.org/yay-git.git
>cd yay-git
>makepkg -si

>yay
>yay -S ttf-ms-fonts google-chrome slack-desktop escrotum-git zoom

// ===[ XINITRC ]=== //
xrdb -merge ~/.Xresources
exec i3

// ===[ zprofile ]=== //
if [[ ! $DISPLAY && $XDG_VTNR -eq 1 ]]; then
  exec startx
fi

// ===[ SHELL ]=== //
1. Change shell to zsh
>chsh -s $(which zsh)

2. Get Oh-My-Zsh
>sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

3. Plugins
>nano ~/.zshrc
>ZSH_THEME="agnoster"
>git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
>git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:=~/.oh-my-zsh/custom}/plugins/zsh-completions
>git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

// ===[ MY DOT FILES ]=== //
https://github.com/codekcv/dotfiles