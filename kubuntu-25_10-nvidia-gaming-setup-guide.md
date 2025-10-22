# Nvidia Gaming Setup for Kubuntu 25.10 "Questing Quokka"

# Nvidia Drivers Install

**Important:** Disable secure boot in your bios. If you use Secure Boot, you might need to enroll MOK keys for the NVIDIA modules.

Reboot the system before installing the drivers (especially if you just updated the kernel).

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

### Add Nvidia GPG Key
We use the Nvidia Debian 12 repo until Nvidia publishes a repo for Debian 13
```bash
sudo apt install curl -y # Ensure curl is installed
curl -fsSL https://developer.download.nvidia.com/compute/cuda/repos/debian12/x86_64/3bf863cc.pub | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/nvidia-cuda.gpg

sudo apt update
```

### Add NVIDIA repo
```bash
echo "deb https://developer.download.nvidia.com/compute/cuda/repos/debian12/x86_64/ /" | sudo tee /etc/apt/sources.list.d/nvidia-cuda.list

sudo apt update
```

### Install kernel headers and build tools
```bash
sudo apt install linux-headers-amd64 linux-headers-$(uname -r) build-essential dkms -y
```

### Install NVIDIA Open driver
nvidia-open: NVIDIA Driver meta-package, Open GPU kernel modules, latest version Meta-package containing all the available packages related to the NVIDIA driver.modules.

```bash
sudo apt install nvidia-open -y
```

### Reboot
```bash
sudo reboot
```

### Verify Nvidia Driver Install
```bash
nvidia-smi

sudo dmesg | grep nvidia
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
