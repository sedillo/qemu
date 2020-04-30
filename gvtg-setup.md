# Install GVTg on Ubunut
  
## Install Ubuntu
Install Ubuntu 18.04.4 (Fresh Install Recommended)
* Minimal Installation

## Install Docker
```
wget http://get.docker.io -O docker.sh
chmod +x docker.sh
sudo ./docker.sh
```

## Download Source
```
sudo apt install -y git
git clone http://github.com/sedillo/qemu
```

## Build and Install qemu
```
cd ~/qemu
sudo docker build -t qemu420 -f Dockerfile-qemu420 .
sudo docker run --rm -it -v $(pwd):/tmp/mount qemu420 cp /root/qemu420.tar.gz /tmp/mount
tar xvf qemu420.tar.gz
cd qemu420
sudo cp -r * /usr/
```

## Update OS

First you must move install the idv patch files to ~/qemu folder

```
cd ~/qemu 
sudo docker build -t kernel419 -f Dockerfile-kernel419 .
sudo docker run --rm -it -v $(pwd):/tmp/mount kernel419 /bin/bash -c 'cp *.deb /tmp/mount/'

sudo su
sed -i "s/GRUB_CMDLINE_LINUX=\"/GRUB_CMDLINE_LINUX=\"i915.enable_gvt=1 intel_iommu=on /g" /etc/default/grub
update-grub

echo -e "\nkvmgt\nvfio-iommu-type1\nvfio-mdev\n" >> /etc/initramfs-tools/modules
update-initramfs -u -k all

reboot
```

## Verify Installation
Verify that these folders/files exist
* /sys/bus/pci/devices/0000:00:02.0/mdev_supported_types
* /sys/class/drm/card0$ ls /sys/class/drm/card0/gvt*
