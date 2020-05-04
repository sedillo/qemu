# Install GVTg on Ubuntu

This set-up is split into a build and target section. For when target/edge devices may be limited.

The same steps will work if all instructions are executed on the same machine.

# Build Machine

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
```
This will result in the file qemu420.tar.gz which should be transferred to a target.

## Build Kernel 
```
cd ~/qemu 
sudo docker build -t kernel419 -f Dockerfile-kernel419 .
sudo docker run --rm -it -v $(pwd):/tmp/mount kernel419 /bin/bash -c 'cp *.deb /tmp/mount/'
```
This will result in the deb files (ls *.deb) which should be transferred to a target.

# Target Machine

## Install Ubuntu
Install Ubuntu 18.04.4 (Fresh Install Recommended)
* Minimal Installation

## If you had a separate build machine execute these commands
```
sudo apt install -y git
git clone http://github.com/sedillo/qemu
```
Copy the qemu and *.deb files to the qemu folder on target machine.

## Install qemu and kernel
Copy the idv (idv2.1_er6_patchset_updated.tar.gz) patch file to qemu folder

```
#Install qemu
cd ~/qemu
tar xvf qemu420.tar.gz
cd qemu
sudo cp -r * /usr/
sudo apt install -y libaio-dev libsdl2-dev libspice-server-dev 

#Install kernel
cd ~/qemu
sudo dpkg -i *.deb

#Update OS to boot kernel
sudo su
sed -i "s/GRUB_CMDLINE_LINUX=\"/GRUB_CMDLINE_LINUX=\"i915.enable_gvt=1 intel_iommu=on /g" /etc/default/grub
sed -i "s/GRUB_TIMEOUT\=.*/GRUB_TIMEOUT\=-1/g" /etc/default/grub
sed -i "s/GRUB_TIMEOUT_STYLE\=.*/GRUB_TIMEOUT_STYLE\=menu/g" /etc/default/grub
update-grub

echo -e "\nkvmgt\nvfio-iommu-type1\nvfio-mdev\n" >> /etc/initramfs-tools/modules
update-initramfs -u -k all

reboot
```

## Verify Installation
On reboot you will see a GRUB menu
* Pick Advanced Installation
* Pick the 4.19 kernel

Verify that these folders/files exist
* /sys/bus/pci/devices/0000:00:02.0/mdev_supported_types
* ls /sys/class/drm/card0/gvt*
