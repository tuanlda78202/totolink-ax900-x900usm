# 📡 Realtek RTL8831 Driver Installation and Setup Guide

---

## 🔧 Prerequisites

| Package | Description |
|---------|-------------|
| `git` | Version control system for cloning the repository |
| `dkms` | Dynamic Kernel Module Support |
| `build-essential` | Development tools and compilers |
| `linux-headers` | Kernel headers for your current kernel |

### Installation Commands

**Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install git dkms build-essential linux-headers-$(uname -r)
```

**CentOS/RHEL/Fedora:**
```bash
# CentOS/RHEL
sudo yum install git dkms gcc make kernel-headers kernel-devel

# Fedora
sudo dnf install git dkms gcc make kernel-headers kernel-devel
```

**Arch Linux:**
```bash
sudo pacman -S git dkms base-devel linux-headers
```

---

## 🚀 Installation Process

### Step 1: Clone the Repository

Download the RTL8831 driver source code:

```bash
git clone https://github.com/heesn/rtl8831.git
cd rtl8831
```

### Step 2: Add DKMS Module

Register the module with DKMS:

```bash
sudo dkms add .
```

### Step 3: Navigate to DKMS Directory

```bash
cd /usr/src/rtl8851bu-0.2
```

### Step 4: Configure Makefile Path

**Find the absolute path:**
```bash
cd ./include
pwd
# Note the output path (e.g., /usr/src/rtl8851bu-0.2/include)
cd ..
```

**Edit the Makefile:**
```bash
sudo nvim Makefile
```

**Update this line:**
```makefile
# Change from:
EXTRA_CFLAGS += -I$(src)/include

# Change to (using your actual path):
EXTRA_CFLAGS += -I/usr/src/rtl8851bu-0.2/include
```

### Step 5: Modify TX Power Function

**Edit the configuration file:**
```bash
sudo nano os_dep/linux/ioctl_cfg80211.c
```

**Find and replace the `cfg80211_rtw_get_txpower` function:**

```c
static int cfg80211_rtw_get_txpower(struct wiphy *wiphy,
                                   struct wireless_dev *wdev,
                                   unsigned int some_val,
                                   int *dbm)
{
    RTW_INFO("%s\n", __func__);
    
    *dbm = 12;  // Hardcoded to 12 dBm
    
    return 0;
}
```

### Step 6: Build and Install

**Build the module:**
```bash
sudo dkms build rtl8851bu/0.2
```

**Install the module:**
```bash
sudo dkms install rtl8851bu/0.2
```

**Load the module:**
```bash
sudo modprobe 8851bu
```

### Step 7: Enable Auto-loading

Create a configuration file to automatically load the driver at boot:

```bash
echo "8851bu" | sudo tee /etc/modules-load.d/8851bu.conf
```

### Step 8: Verify Installation

**Check network interfaces:**
```bash
ip a
# Look for a new wireless interface (usually wlan0 or similar)
```

**Verify module is loaded:**
```bash
lsmod | grep 8851bu
```

### Step 9: Restart and Connect

**Restart your system:**
```bash
sudo reboot
```

After reboot, you should be able to connect to wireless networks through your system's network manager.

---

## ✅ Verification and Troubleshooting

### Quick Status Checks

```bash
# Check if module is loaded
lsmod | grep 8851bu

# Check DKMS status
sudo dkms status

# List wireless interfaces
iwconfig

# Check wireless device recognition
lsusb | grep -i realtek
```

### System Logs

```bash
# Check kernel messages
dmesg | grep -i rtl

# Monitor wireless activity
journalctl -f | grep -i wireless

# Check system messages
sudo tail -f /var/log/messages
```

---

## 🔍 Common Issues and Solutions

| Issue | Solution |
|-------|----------|
| **Module fails to build** | Install kernel headers: `sudo apt install linux-headers-$(uname -r)` |
| **Interface not appearing** | Reload module: `sudo modprobe -r 8851bu && sudo modprobe 8851bu` |
| **Compilation errors** | Install development packages: `sudo apt install build-essential` |
| **DKMS build fails** | Check kernel version compatibility |
| **No wireless networks visible** | Restart NetworkManager: `sudo systemctl restart NetworkManager` |

### Advanced Troubleshooting

**Check hardware detection:**
```bash
# List USB devices
lsusb

# Check PCI devices
lspci | grep -i network

# Verify driver binding
ls /sys/class/net/
```

---

## 🗑️ Uninstallation

If you need to remove the driver completely:

```bash
# Stop and remove the module
sudo modprobe -r 8851bu

# Remove from DKMS
sudo dkms remove rtl8851bu/0.2 --all

# Remove auto-loading configuration
sudo rm -f /etc/modules-load.d/8851bu.conf

# Clean up source files (optional)
sudo rm -rf /usr/src/rtl8851bu-0.2
```

---

## 📝 Important Notes

- **Chipset Compatibility**: This driver is specifically designed for Realtek RTL8831 and RTL8851 chipsets
- **TX Power**: The transmission power is hardcoded to 12 dBm in the modified function
- **Backup Recommendation**: Always create a system backup before installing third-party drivers
- **Distribution Support**: Commands are optimized for Debian/Ubuntu; adjust package names for other distributions
- **Kernel Updates**: You may need to rebuild the driver after kernel updates

---

## 🔗 Additional Resources

- [Original Repository](https://github.com/heesn/rtl8831)
- [DKMS Documentation](https://github.com/dell/dkms)
- [Linux Wireless Documentation](https://wireless.wiki.kernel.org/)
