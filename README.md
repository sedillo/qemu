# Qemu and kernel build instructions

## Getting Started

Docker build commands will build qemu and kernel files.
Docker run commands will copy from docker container to local folder.

### Build and copy process

Build qemu 4.20

```
docker build -t qemu420 -f Dockerfile-qemu420 .
docker run --rm -it -v $(pwd):/tmp/mount qemu420 cp /root/qemu420.tar.gz /tmp/mount
```

Build Kernel 4.19

Copy the file idv2.1_er6_patchset.tar.gz into the same directory as the Dockerfile.
Name must match exactly.

```
docker build -t kernel419 -f Dockerfile-kernel419 .
docker run --rm -it -v $(pwd):/tmp/mount kernel419 /bin/bash -c 'cp *.deb /tmp/mount/'
```

