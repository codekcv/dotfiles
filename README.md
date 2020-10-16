This is a personal guide for my own arch install for my reference only.<br/>
Installation depends on your hardware and personal preferences.<br/>
You may use my dotfiles and configs if you want.

## setup
```
os: arch linux
shell: zsh
terminal: alacritty
wm: i3-gaps (planning to go bspwm)
switcher: rofi
bar: polybar
compositor: picom
display server: xorg
editor: nvim / vscode
```

NOTE:
> If you're installing arch, please follow the [official guide](https://wiki.archlinux.org/index.php/Installation_guide) instead.

// --- Preparation
1. Download [Arch ISO](https://www.archlinux.org/download/) & [Balena Etcher](https://www.balena.io/etcher/)<br/>
2. Flash to drive and boot to UEFI

// --- Installation 
1. check stuff<br/>
1.1 internet `> ping -c3 archlinux.org`<br/>
1.2 isUEFI? `> ls /sys/firmware/efi/efivars`<br/>
1.3 disk `> fdisk -l`

2. update sys clock `> timedatectl set-ntp true`

3. wipe drive and create partitions<br/>
3.1 `> gdisk /dev/sd*`<br/>
3.2 o -> n -> defx2, +512MB, ef00 -> n -> defx3 -> w

4. format partition<br/>
4.1 boot `> mkfs.fat /dev/sd*1`<br/>
4.2 system `> mkfs.ext4 /dev/sd*2`

5. Mount<br/>
```
> mount /dev/sd*2 /mnt // system 1st
> mkdir /mnt/boot/efi
> mount /dev/sd*1 /mnt/boot/efi
```

// --- Init Packages<br/>
`> pacstrap /mnt ...`

* System Packages
> base base-devel linux linux-firmware grub efibootmgr networkmanager man amd-ucode xf86-video-amdgpu udisks2 ntfs-3g alsa-utils pulseaudio-alsa pavucontrol

* Environment Packages
> xorg xorg-xinit xorg-xrdb alacritty i3-gaps polybar rofi picom nitrogen neofetch htop bspwm nautilus eog noto-fonts-emoji

* Tool Packages
> nvim git zsh npm p7zip gnome-keyring libsecret

* Normal Apps
> vlc qbittorrent gimp libreoffice-still obs-studio gedit discord handbrake kdenlive 

// --- Post Configs
1. Fstab `> genfstab -U /mnt >> /mnt/etc/fstab`

2. chroot `> arch-chroot /mnt /bin/bash`

3. network `> systemctl enable NetworkManager`

4. bootloader<br/>
4.1 `> grub-install /dev/sd*`<br/>
4.2 `> grub-mkconfig -o /boot/grub/grub.cfg`

5. timezone<br/>
5.1 `> ln -sf /usr/share/zoneinfo/Singapore /etc/localtime`<br/>
5.2 `> hwclock --systohc`

6. locale<br/>
6.1 `> nvim /etc/locale.gen`<br/>
6.2. get en_US.UTF-8 UTF-8<br/>
6.3. `> locale-gen`<br/>
6.4. `> nvim /etc/locale.conf`<br/>
6.5. LANG=en_US.UTF-8

7. hostname<br/>
7.1. `> nvim /etc/hostname` -> [name]<br/>
7.2. `> nvim /etc/hosts` <br/>
```
127.0.0.1 localhost
::1 localhost
127.0.1.1 arch.localdomain arch
```

8. account<br/>
8.1. Set root ps. `> passwd`<br/>
8.2. create user
```
> useradd -m -G wheel [name]
> passwd [name]
> nvim /etc/sudoers // uncomment wheel all
```
9. fin
```
> exit
> umount -R /mnt
> reboot
```

#### on user account
// --- AUR helper
```
> sudo git clone https://aur.archlinux.org/yay-git.git
> cd yay-git
> makepkg -si
> yay -S ...
```

* AUR packages
> tts-fs-fonts google-chrome visual-studio-code-bin neovim-nightly viber slack-desktop  escrotum-git 4kvideodownloader 4kyoutubetomp3 zoom

// --- xinitrc
```
xrdb -merge ~/.Xresources
exec i3
```

// --- zprofile
```
if [[ ! $DISPLAY && $XDG_VTNR -eq 1 ]]; then
  exec startx
fi
```

// --- shell
1. change shell to zsh `> chsh -s $(which zsh)`

2. get Oh-My-Zsh<br/>
`> sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`

3. plugins<br/>
```
> git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
> git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:=~/.oh-my-zsh/custom}/plugins/zsh-completions
> git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

4. theme [agnoster](https://github.com/agnoster/agnoster-zsh-theme)

// --- multilib<br/>
1. `> sudo nvim /etc/pacman.conf`<br/>
3. enable
```
[multilib]
Include = /etc/pacman.d/mirrorlist
```
3. sys upgrade `> sudo pacman -Syu`

// ===[ MY DOT FILES ]=== // <br/>
https://github.com/codekcv/dotfiles
