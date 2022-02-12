# Vagrant blank box

An empty box to test PXE booting over the network, or booting from local ISO files. And possibly attach USB devices, or allocate some storage on local attached disks.

Our goal is to have a virtual box with 2 vCPU, 2 GB RAM, 2x CD/DVD ROM drives, 4x network adapters, 4x storage devices, and an enabled USB controller. The boot order should be:

- Network
- Removable devices
- CD-ROM drive
- Hard drive

The hardware should be compatible with Linux 3.x, which is good as far back as CentOS 7 and Ubuntu 16.04.
