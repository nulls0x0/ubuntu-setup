# General Gaming Setup for Kubuntu 25.10 "Questing Quokka"

### Install Google Chrome

```bash
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt install ./google-chrome-stable_current_amd64.deb
```

# Part I: Drivers

### Add 32-bit platform support
This ensures 32-bit drivers will be installed which are required for Steam later.
```bash
sudo dpkg --add-architecture i386
sudo apt update && sudo apt upgrade -y
```

### Install kernel headers and build tools
```bash
sudo apt install linux-headers-$(uname -r) build-essential dkms -y
```

## AMD Drivers Install
Follow these steps only if you have an AMD GPU.

### Install latest stable version of Mesa driver
```bash
sudo apt install mesa-utils
glxinfo | grep "OpenGL version"

sudo add-apt-repository ppa:kisak/kisak-mesa
sudo apt update
sudo apt upgrade
```

## Nvidia Drivers Install
Follow these steps only if you have an Nvidia GPU.

### Add the graphics-drivers PPA
This PPA provides the latest NVIDIA drivers for Ubuntu-based distributions:
```bash
sudo add-apt-repository ppa:graphics-drivers/ppa -y
sudo apt update
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

### Ubuntu Nvidia Driver Signing (Secure Boot)
When you install an NVIDIA driver from the PPA, Ubuntu automatically detects the need to sign third-party modules when Secure Boot is enabled.
A unique local signing key, called a Machine Owner Key (MOK), should automatically be generated. The NVIDIA kernel modules are then automatically signed with this newly generated MOK.
Ubuntu should detect that the MOK has not been enrolled in your UEFI firmware, and it will prompt you to set a password during the driver installation process.
After the first reboot, you should be presented with the MokManager blue screen before the system fully boots.
Select "Enroll MOK" and use the password you set during the driver installation.
Once the key is enrolled, Ubuntu will trust any module signed with that key, and the driver will load correctly with Secure Boot enabled.

### Reboot
Reboot the system to load the Nvidia driver.

```bash
sudo reboot
```

# Part II: Applications

## Remove Snaps

If you performed the minimal install, there shouldn't be any snaps installed. List the snaps currently installed on your system:

```bash
snap list
```

### Block Snaps

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
The ntsync (NT Synchronization Primitive) kernel module is a specialized driver for Linux 
designed to significantly improve the performance and compatibility of Windows applications, 
particularly games, when run through Proton and Wine.

To enable the kernel module to load at boot:
```bash
echo 'ntsync' | sudo tee /etc/modules-load.d/ntsync.conf

# Loads the module immediately without a reboot
sudo modprobe ntsync

# Verify the module is loaded
lsmod | grep ntsync
```

## Install non-free video codecs
```bash
sudo apt install ubuntu-restricted-extras
sudo apt install vainfo
vainfo
```

### Reboot
Reboot the system to load the modifications we have made.

```bash
sudo reboot
```

## Flatpak

Flatpak is another containerized package format that some users prefer:

### Install Flatpak

```bash
sudo apt install flatpak plasma-discover-backend-flatpak -y
```

### Add Flathub Repository

```bash
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

## Chrome Browser: Hardware Accelerated Video
To help reduce power usage and make our system more efficient when watching video using Chrome, we must add some launch options.

Intel: 
```
--enable-features=AcceleratedVideoDecodeLinuxZeroCopyGL,AcceleratedVideoDecodeLinuxGL,AcceleratedVideoEncoder
```
AMD: 
```
--enable-features=AcceleratedVideoDecodeLinuxZeroCopyGL,AcceleratedVideoDecodeLinuxGL,VaapiIgnoreDriverChecks
```
Nvidia: 
```
--enable-features=AcceleratedVideoDecodeLinuxZeroCopyGL,AcceleratedVideoDecodeLinuxGL,VaapiIgnoreDriverChecks,VaapiOnNvidiaGPUs
```

The steps below use the AMD launch options. Substitute the correct options based on your GPU model.

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

## Install Steam
```bash
sudo apt install steam-installer -y
```

# Gaming Applications Setup

### ProtonUp-Qt (Flatpak)
1. Open **Discover Software Manager**
2. In the search input enter **proton**
3. Select the **ProtonUp-Qt** Flatpak (From Flathub) package
4. Click **Install**

### Minecraft Launcher 'Prism Launcher' (Flatpak)
1. Open **Discover Software Manager**
2. In the search input enter **prism**
3. Select the **Prism Launcher** Flatpak (From Flathub) package
4. Click **Install**

### Roblox Launcher 'Sober' (Flatpak)
1. Open **Discover Software Manager**
2. In the search input enter **sober**
3. Select the **Sober** Flatpak (From Flathub) package
4. Click **Install**

### OBS Studio (Flatpak)
1. Open **Discover Software Manager**
2. In the search input enter **obs**
3. Select the **OBS Studio** Flatpak (From Flathub) package
4. Click **Install**

# Part III: KDE Desktop Tweaks
### Set Maximum Refresh Rate for Your Display

Ensuring your display is set to its maximum refresh rate is crucial for smooth gaming. Higher refresh rates provide a better gaming experience with reduced input lag and smoother motion.

1. Open **System Settings**
2. Go to **Display & Monitor**
3. Select your monitor
4. Under **Refresh rate**, select the highest available option (e.g., 144 Hz, 165 Hz, 240 Hz)
5. Click **Apply**

### Enable Gsync or FreeSync

To enable variable refresh rates for your games.

1. Open **System Settings**
2. Go to **Display & Monitor**
3. Select your gaming monitor
4. Under **Adaptive sync**, select the **Automatic** or **Always** option
5. Under **Screen tearing** check **Allow in fullscreen windows**
6. Click **Apply**

Automatic: 
Adaptive Sync is only enabled when an opaque fullscreen window is in focus. This is typically a full-screen game or a full-screen video player. Adaptive Sync will not be active for windowed games, desktop navigation, or other non-fullscreen content.

Always:
Adaptive Sync is permanently enabled. The display's refresh rate will constantly adjust to match the frame rate of the entire screen, including desktop elements, windows, and video content. It can cause the screen to noticeably flicker or feel sluggish with low-framerate content.

### Disable Mouse Acceleration

Mouse acceleration can negatively impact gaming performance, especially in FPS games where precise aim is crucial. Disabling it provides consistent 1:1 mouse movement.

1. Open **System Settings**
2. Go to **Mouse and Touchpad**
3. Select **Mouse**
4. Select your mouse in the **Device** drop down
5. Un-check the **Enable pointer acceleration** check box
6. Click **Apply**

### Enable CPU Performance Governor

For better gaming performance, you can set your CPU governor to performance mode:

1. On the Taskbar left click **Show hidden icons** arrow
2. Open **Power Management**
3. Move the **Power Profile** slider to **Performance**

## Installing Gamemode for on-demand performance
```bash
sudo apt install gamemode -y
```
   
### Enable Gamemode for Steam Games
To enable gamemode for your Steam games, you need to add a launch option to each game:

**For Individual Games:**
1. Open your Steam Library
2. Right-click on the game you want to optimize
3. Select **Properties**
4. In the **Launch Options** field, add:
   ```
   gamemoderun %command%
   ```
5. Close the properties window and launch the game

### Makes the system prefer using RAM over disk swap
```bash
sudo sysctl vm.swappiness=10
echo 'vm.swappiness=10' | sudo tee /etc/sysctl.d/99-swappiness.conf > /dev/null
```
