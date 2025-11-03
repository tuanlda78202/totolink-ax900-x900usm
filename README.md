# totolink ax900 x900usm linux driver

![TOTOLINK](https://github.com/user-attachments/assets/81cd186a-665d-4513-8330-98189fd4655a)


> [!NOTE]  
> support ubuntu 24.04 (linux kernel 6.14)

## install

1. update system and install dependencies

```bash
sudo apt update
sudo apt install git dkms build-essential linux-headers-$(uname -r)
```

2. download src

```
git clone https://github.com/tuanlda78202/totolink-ax900.git
cd totolink-ax900 
```

3. add dkms module

```bash
sudo dkms add . 
```

4. navigate dkms dir and config makefile path  

```bash
cd /usr/src/rtl8851bu-0.2 && cd ./include
cd ..
```

5. update makefile

```bash
sudo vim Makefile

# update this line 
# EXTRA_CFLAGS += -I$(src)/include
# to
# EXTRA_CFLAGS += -I/usr/src/rtl8851bu-0.2/include
```

6. build and install

```bash
sudo dkms build rtl8851bu/0.2
sudo dkms install rtl8851bu/0.2
sudo modprobe 8851bu
```

7. auto-loading at boot

```bash
echo "8851bu" | sudo tee /etc/modules-load.d/8851bu.conf
```

8. verify installation

```bash
# new wireless interface
ip a

# verify module is loaded
lsmod | grep 8851bu
```

9. reboot

```bash
sudo reboot
```

## troubleshooting

```bash
# list usb devices
lsusb

# check pci devices 
lspci | grep -i network

# verify driver binding 
ls /sys/class/net/
```

## uninstallation

```bash
# stop and remove the module
sudo modprobe -r 8851bu

# remove from DKMS
sudo dkms remove rtl8851bu/0.2 --all

# remove auto-loading configuration
sudo rm -f /etc/modules-load.d/8851bu.conf

# clean up source files
sudo rm -rf /usr/src/rtl8851bu-0.2
```

## acks

* [heesn/rtl8831](https://github.com/heesn/rtl8831)
