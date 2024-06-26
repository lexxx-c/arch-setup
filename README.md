# arch-setup
Arch install, setups, and gotchas

[ArchWiki - Installation Guide](https://wiki.archlinux.org/title/installation_guide`)

[nice and quite comprehensive guide](https://www.liquidweb.com/kb/arch-linux-installation-guide/)

## Pre-installation (live CD)

### Set the console keyboard layout and font (live)
for my 32''

    setfont ter-120b
(fonts up to `ter-132b`)

### Connect to the internet (live)
[ArchWiki - iwd](https://wiki.archlinux.org/title/Iwd)

    iwctl
once in the iwd `[iwd]#` prompt

    station <name> scan
    station <name> get-networks
    station <name> connect <SSID>
    quit
check live network

    ping -c 3 archlinux.org
    ping -c 3 8.8.8.8

### Update the system clock (or not?)
    timedatectl


### Partition the disks
check disks and partitons
    fdisk -l
#### cli
    fdisk /dev/<the_disk_to_be_partitioned>
once in the fdisk `Command (m for help):` prompt
##### print partitions
    p
##### create a new partition table, if needed
    o
##### delete old partitions, if needed
    d
##### create new partition
    n
(all primary, default)
##### last sector for EFI
    +1024M
##### set partition type
    t
had different outputs for _EFI_ from `l` (possibly due to using upper and lower L inconsistently?)

either `1` or `ef` (exadecimal really shouldn't care if upper or lower!)

no problems for _Linux Filesystem_, which should be the default anyway

#### tui
`TODO` look for it and try it out

    cfdisk /dev/<the_disk_to_be_partitioned>
`NOTE` new partition table not tried

#### SWAP
`TODO`! it'll be a swap file -> [ArchWiki - Swap#Swap_file](https://wiki.archlinux.org/title/Swap#Swap_file)

### Format the partitions
    mkfs.fat -F 32 /dev/<efi_system_partition>
    mkfs.ext4 /dev/<root_partition>

### Mount the file systems
    mount /dev/<root_partition> /mnt
    mount --mkdir /dev/<efi_system_partition> /mnt/boot

## Install essential packages
    pacstrap -K /mnt base linux linux-firmware networkmanager neovim 
_need_ an editor _and_ a network manager

the rest will be installed after reboot

with an old-ish live iso

    pacman-key --init
    pacman-key --refrsh-keys
probably there better ways (surely a fresh sio is one)

## Configure the system
### Fstab
    genfstab -U /mnt >> /mnt/etc/fstab
(or `-L` to define by labels)
### Chroot
    arch-chroot /mnt
### Time
    ln -sf /usr/share/zoneinfo/Europe/London /etc/localtime
    hwclock --systohc
### Localization
    nvim /etc/locale.gen
uncomment `en_GB.UTF-8 UTF-8`

    locale-gen
to generate the locales, then

    echo LANG=en_GB.UTF-8 > /etc/locale.conf
### Network configuration 
    echo <my_host_name> > /etc/hostname
this can be done later with the networkmanager tui

### Root password
    passwd

### Boot loader
#### GRUB
[ArchWiki - GRUB](https://wiki.archlinux.org/title/GRUB)

    pacman -S grub efibootmgr
    grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
_why so easy to miss this?_

    grub-mkconfig -o /boot/grub/grub.cfg
`TODO`: learn GRUB customization

#### systemd-boot
`TODO`: learn and evaluate

### Reboot
`exit` or `Ctrl+d`

    reboot



## General setup

### create user
    useradd -m -G wheel <username>
    passwd <username>
#### enable sudo for user
[ArchWiki - Sudo](https://wiki.archlinux.org/title/Sudo)

    pacman -S --needed sudo
    EDITOR=nvim visudo
To allow members of group wheel sudo access, uncomment:

    %wheel      ALL=(ALL:ALL) ALL
### Networking
    systemctl enable NetworkManager
    systemctl start NetworkManager
    nmtui
`nmtui`, so simple but undocumented?

### hyprland
    pacman -S hyprland kitty gtk4


### development - languages
most will come up from nvvim `:checkhealth`, like `unzip`, `wget`, etc.

some specifics
#### rust 
    pacman -S rustup
    rustup default stable
in favour of `rust` + `cargo`
#### nvm (node version manager)
    yay -S nvm
    source /usr/share/nvm/init-nvm.sh
    source /usr/share/nvm/nvm.sh
    nvm install --lts
`TODO` add config to .bashrc


#### lua
    pacman -S luarocks
#### go
    pacman -S go
(also needed for yay)


### bluetooth
    pacman -s --needed bluez bluez-utils
prompt

    bluetoothctl
once in the prompt
    
    scan on
    pair <device_mac>
    connect <device_mac>
    trust <device_mac>
    exit
autocomplete works, use for mac addresses
[ArchWiki - bluetooth](https://wiki.archlinux.org/title/bluetooth)

[bluez](https://www.bluez.org/)

### 

### yay (see paru)
    sudo pacman --needed base-devel git go
    cd ~/.config/
    mkdir yay
    git clone https://aur.archlinux.org/yay.git
    cd yay
    makepkg -sri
[AUR - yay](https://aur.archlinux.org/packages/yay)

### paru

[AUR - paru](https://aur.archlinux.org/packages/paru)

### neovim
#### nerd fonts
    pacman -S ttf-hack-nerd
[ttf-hack-nerd](https://archlinux.org/packages/extra/any/ttf-hack-nerd/)

#### dependencies
    pacman -S --needed ripgrep fg

### Hyprland

#### screen resolution
    hyprctl monitors
to see current monitors setup (all auto if still from autogenerated)

in `~/.config/hypr/hypr.conf` (based on my current monitors and layout)

    monitor = HDMI-A-1, 3840x2160, 0x0, 1.20
    monitor = DVI-D-1, 1920x1080, -1920x0, 0.80
these settings are: _monitor_name, resolution, position, scaling_ 

also possible to rotate with additional flags after the above

### python
#### pynvim
    pacman -S --needed python-pynvim
`TODO` consider changing nvim configs for pynvim
#### pyenv
    pacman -S --needed pyenv
`TODO` review setup and document here





