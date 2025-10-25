# Nvidia Gaming Setup for Kubuntu 25.10 "Questing Quokka"

# Nvidia Drivers Install

**Important:** Disable secure boot in your bios. If you use Secure Boot, you might need to enroll MOK keys for the NVIDIA modules. 
Refer to the Ubuntu documentation for "Secure Boot" if you encounter issues after driver installation.

Reboot the system before installing the drivers (especially if you just updated the kernel).

### Add 32-bit platform support
This ensures 32bit drivers will be installed which are required for Steam later.
```bash
sudo dpkg --add-architecture i386
sudo apt update && sudo apt upgrade -y
```

### Add the graphics-drivers PPA
This PPA provides the latest NVIDIA drivers for Ubuntu-based distributions:
```bash
sudo add-apt-repository ppa:graphics-drivers/ppa -y
sudo apt update
```

### Install kernel headers and build tools
These are required for building the NVIDIA kernel modules:
```bash
sudo apt install linux-headers-$(uname -r) build-essential dkms -y
```

### Install NVIDIA Open Kernel Driver
The NVIDIA open kernel driver provides better performance and integration with the Linux kernel.
This is recommended for RTX 20 series (Turing) and newer GPUs.

First, check what drivers are available for your system:
```bash
ubuntu-drivers devices | grep 'nvidia-driver.*-open'
```

Then install the latest NVIDIA open driver: i.e. nvidia-driver-[version]-open
If you aren't sure which to select, use the driver with 'recommended' tagged on the end
```bash
# Install the latest available NVIDIA open driver
sudo apt install nvidia-driver-[version]-open -y
```

### Ubuntu Driver Siging (Secure Boot)
When you install an NVIDIA driver from the PPA, Ubuntu automatically detects the need to sign third-party modules when Secure Boot is enabled.
A unique local signing key, called a Machine Owner Key (MOK), should automatically be generated. The NVIDIA kernel modules are then automatically signed with this newly generated MOK.
Ubuntu should detect that the MOK has not been enrolled in your UEFI firmware, and it will prompt you to set a password during the driver installation process.
After the first reboot, you should be presented with the MokManager blue screen before the system fully boots.
Select "Enroll MOK" and use the password you set during the driver installation.
Once the key is enrolled, Ubuntu will trust any module signed with that key, and the driver will load correctly with Secure Boot enabled.

### Reboot
```bash
sudo reboot
```

### Verify Nvidia Driver Install
```bash
nvidia-smi
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

### Install Google Chrome

```bash
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt install ./google-chrome-stable_current_amd64.deb
```

### Chrome: Setup Hardware Accelerated Video Decode

Add these options to the Chrome launcher
```bash
cp /usr/share/applications/google-chrome.desktop ~/.local/share/applications/
nano ~/.local/share/applications/google-chrome.desktop

# Look for the lines starting with Exec= and add these launch options
--enable-features=AcceleratedVideoDecodeLinuxZeroCopyGL,AcceleratedVideoDecodeLinuxGL,VaapiIgnoreDriverChecks,VaapiOnNvidiaGPUs,AcceleratedVideoEncoder

# Here is a scripted method
FILE_PATH=~/.local/share/applications/google-chrome.desktop
FLAGS=" --enable-features=AcceleratedVideoDecodeLinuxZeroCopyGL,AcceleratedVideoDecodeLinuxGL,VaapiIgnoreDriverChecks,VaapiOnNvidiaGPUs,AcceleratedVideoEncoder"
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
