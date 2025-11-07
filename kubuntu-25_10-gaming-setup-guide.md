# General Gaming Setup for Kubuntu 25.10 "Questing Quokka"

### Install Google Chrome

```bash
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt install ./google-chrome-stable_current_amd64.deb
```

### Add 32-bit platform support
This ensures 32bit drivers will be installed which are required for Steam later.
```bash
sudo dpkg --add-architecture i386
sudo apt update && sudo apt upgrade -y
```

### Install kernel headers and build tools
```bash
sudo apt install linux-headers-$(uname -r) build-essential dkms -y
```

### Install latest stable version of Mesa driver
```bash
sudo apt install mesa-utils
glxinfo | grep "OpenGL version"

sudo add-apt-repository ppa:kisak/kisak-mesa
sudo apt update
sudo apt upgrade
```

### Install non-free video codecs
```bash
sudo apt install ubuntu-restricted-extras
sudo apt install vainfo
vainfo
```

## List Installed Snaps

First, check which Snap packages are currently installed on your system:

```bash
snap list
```

## Prevent Snapd from Being installed

Create an APT preference file to prevent snapd from being reinstalled automatically:

```bash
sudo tee /etc/apt/preferences.d/nosnap.pref <<EOF
Package: snapd
Pin: release a=*
Pin-Priority: -10
EOF
```

Update your package lists:

```bash
sudo apt update
```

## Enable NTSYNC Kernel Module
The ntsync (NT Synchronization Primitive) kernel module is a specialized driver for Linux designed to significantly improve the performance and compatibility of Windows applications, particularly games, when run through compatibility layers like Proton.
To enable the kernel module to load at boot:
```bash
echo 'ntsync' | sudo tee /etc/modules-load.d/ntsync.conf

# Loads the module immediately without a reboot
sudo modprobe ntsync

# Verify the module is loaded
lsmod | grep ntsync
```

## Flatpak

Flatpak is another containerized package format that some users prefer:

#### Install Flatpak

```bash
sudo apt install flatpak plasma-discover-backend-flatpak -y
```

#### Add Flathub Repository

```bash
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

### Chrome: Setup Hardware Accelerated Video Decode

Add these options to the Chrome launcher
```bash
cp /usr/share/applications/google-chrome.desktop ~/.local/share/applications/
nano ~/.local/share/applications/google-chrome.desktop

# Look for the lines starting with Exec= and add these launch options
--enable-features=AcceleratedVideoDecodeLinuxZeroCopyGL,AcceleratedVideoDecodeLinuxGL,VaapiIgnoreDriverChecks,AcceleratedVideoEncoder

# Here is a scripted method
FILE_PATH=~/.local/share/applications/google-chrome.desktop
FLAGS=" --enable-features=AcceleratedVideoDecodeLinuxZeroCopyGL,AcceleratedVideoDecodeLinuxGL,VaapiIgnoreDriverChecks,AcceleratedVideoEncoder"
sed -i 's/ --enable-features=[^ ]*//g' "$FILE_PATH"
sed -i "s/^\(Exec=.*google-chrome-stable\)\(.*\)/\1${FLAGS}\2/g" "$FILE_PATH"
```

# Gaming Setup

### Install Steam
```bash
sudo apt install steam-installer -y
```

### Installing Gamemode for on-demand performance
```bash
sudo apt install gamemode -y
```

### Makes the system prefer using RAM over disk swap
```bash
sudo sysctl vm.swappiness=10
echo 'vm.swappiness=10' | sudo tee /etc/sysctl.d/99-swappiness.conf > /dev/null
```

# Gaming Applications Setup

### ProtonUp-Qt (Flatpak)
1. Open **Discover Software Manager**
2. In the search input enter **proton**
3. Select the **ProtonUp-Qt** Flatpak (Flathub) package
4. Click **Install**

### Minecraft Launcher 'Prism' (Flatpak)

### Roblox Launcher 'Sober' (Flatpak)
