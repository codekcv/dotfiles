This is a personal guide for my own arch install for my reference only.<br/>
Installation depends on your hardware and personal preferences.

// --- Preparation
1. Download [Arch ISO](https://www.archlinux.org/download/) & [Balena Etcher](https://www.balena.io/etcher/)<br/>
2. Flash to drive and boot to UEFI

// --- Installation
1. check stuff<br/>
1.1 internet > `ping -c3 google.com'`<br/>
1.2 isUEFI? > `ls /sys/firmware/efi/efivars`<br/>
1.3 disk > `fdisk -l`

2. update sys clock > `timedatectl set-ntp true`

5. wipe drive and create partitions<br/>
5.1 > `gdisk /dev/sd*`<br/>
5.2 o -> n -> defx2, +512MB, ef00 -> n -> defx3 -> w

6. format partition
6.1 mkfs.fat /dev/sd*1
6.2 mkfs.ext4 /dev/sd*2

7. Mount<br/>
7.1 mount /dev/sd*2 /mnt<br/>
7.2 mkdir /mnt/boot/efi<br/>
7.3 mount /dev/sd*1 /mnt/boot/efi

// ===[ INIT PACKAGES ]=== //
pacstrap /mnt *

// --- System Packages
base base-devel linux linux-firmware grub efibootmgr networkmanager man amd-ucode xf86-video-amdgpu udisks2 ntfs-3g alsa-utils pulseaudio-alsa pavucontrol

// --- Environment Packages
xorg xorg-xinit xorg-xrdb alacritty i3-gaps polybar rofi picom nitrogen neofetch htop bspwm nautilus eog noto-fonts-emoji

// --- Tool Packages
nvim git zsh npm p7zip gnome-keyring libsecret

// --- Normal Apps
vlc qbittorrent gimp libreoffice-still obs-studio gedit discord handbrake kdenlive 

// --- AUR
tts-fs-fonts google-chrome visual-studio-code-bin neovim-nightly viber slack-desktop  escrotum-git 4kvideodownloader 4kyoutubetomp3 zoom

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
1. >nvim /etc/locale.gen
2. uncomment english example = en_US.UTF-8 UTF-8
3. >locale-gen
4. >nvim /etc/locale.conf
5. Type "LANG=en_US.UTF-8

// ===[ HOSTNAME ]=== //
1. >nvim /etc/hostname -> arch
2. >nvim /etc/hosts
127.0.0.1 localhost
::1 localhost
127.0.1.1 arch.localdomain arch

// ===[ ACCOUNTS ]=== //
1. Set root ps. >password

>useradd -m -G wheel [name]
>passwd [name]
>nvim /etc/sudoers -> uncomment wheel all

>exit
>umount -R /mnt
>reboot

// ON ACCOUNT!
// ===[ AUR HELPER ]=== //
>sudo git clone https://aur.archlinux.org/yay-git.git
>cd yay-git
>makepkg -si
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
>nvim ~/.zshrc
>ZSH_THEME="agnoster"
>git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
>git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:=~/.oh-my-zsh/custom}/plugins/zsh-completions
>git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

// ===[ MY DOT FILES ]=== //
https://github.com/codekcv/dotfiles
