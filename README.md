# totolink ax900 x900usm linux driver

![TOTOLINK](https://github.com/user-attachments/assets/81cd186a-665d-4513-8330-98189fd4655a)

> [!NOTE]  
> support ubuntu 24.04 (linux kernel 6.14)

## install

1. install dependencies

```bash
sudo apt update
sudo apt install git dkms build-essential linux-headers-$(uname -r)
```

2. clone src

```
git clone https://github.com/tuanlda78202/totolink-ax900-x900usm.git
cd totolink-ax900-x900usm 
```

3. add dkms module

```bash
sudo dkms add . 
cd /usr/src/rtl8851bu-0.2
```

4. build and install

```bash
sudo dkms build rtl8851bu/0.2
sudo dkms install rtl8851bu/0.2
sudo modprobe 8851bu
```

5. auto-loading at boot

```bash
echo "8851bu" | sudo tee /etc/modules-load.d/8851bu.conf
```

6. verify installation

```bash
ip a
lsmod | grep 8851bu
```

7. reboot

```bash
sudo reboot
```

## troubleshooting

```bash
lsusb
lspci | grep -i network
ls /sys/class/net/
```

## uninstallation

```bash
sudo modprobe -r 8851bu
sudo dkms remove rtl8851bu/0.2 --all
sudo rm -f /etc/modules-load.d/8851bu.conf
sudo rm -rf /usr/src/rtl8851bu-0.2
```

## ref

* [heesn/rtl8831](https://github.com/heesn/rtl8831)
