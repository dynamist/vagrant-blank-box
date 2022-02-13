# Vagrant blank box

An empty box to test PXE booting over the network, or booting from local ISO files. And possibly attach USB devices, or allocate some storage on local attached disks.

Our goal is to have a virtual box with 2 vCPU, 1 GB RAM, 2x CD/DVD ROM drives, 4x network adapters, 4x storage devices, and an enabled USB controller. The boot order should be:

- Removable devices
- CD-ROM drive
- Hard drive
- Network

The hardware should be compatible with Linux 3.x, which is good as far back as CentOS 7 and Ubuntu 16.04.


## Import this box locally

```
vagrant box add blank-box.json --provider vmware_desktop
vagrant box add blank-box.json --provider virtualbox
```


## Oracle Virtualbox

After creating the VM with Virtualbox use Vagrants built-in awesome support for Virtualbox to package it up:

```
vagrant package --base blank --output blank-virtualbox.box
```

The image file is a gzip compressed tarball. For the sake of version control we have extracted the contents into `blank-virtualbox`. And then added a "metadata.json" file because the documentation says it should be there.

<!--
Guide for Virtualbox 5.2.42

Choose to create a new VM

Name and operating system
Name: blank
Type: Linux
Version: Other Linux (64-bit)

Memory size: 1024 MB

Choose "Create a virtual hard disk now"

File location: blank-disk1
File size: 10 GB
Choose VDI
Choose Dynamically allocated

Right click
Choose Settings

System -> Motherboard
Boot order:
1. Check: Floppy
2. Check: Optical
3. Check: Hard Disk
4. Check: Network
Chipset: PIIX3
Pointing Device: USB Tablet
Extended Features:
- Check Enable I/O APIC
- Uncheck EFI
- Check Hardware Clock in UTC Time

System -> Processor
- Check Enable PAE/NX
- Processor(s): 2

System -> Acceleration
Paravirtualization Interface: Default
Hardware Virtualization:
- Check: Enable VT-x/AMD-V
- Check: Enable Nested Paging

Storage -> Make it so:

- Controller: IDE
  - "Empty" optical disc
    Optical Drive: IDE Primary Master
    Check: Live CD/DVD
  - "Empty" optical disc
    Optical Drive: IDE Primary Slave
    Check: Live CD/DVD
- Controller: SATA
  - Name: blank-disk1.vdi
    Dynamically Allocated
    Uncheck: Solid-state Drive
    Uncheck: Hot-pluggable
-->

## VMware Workstation Pro

The lowest common version that supports these features is Workstation 14.x (compatible with ESX1 6.7 and Fusion 10.x)

- NVMe devices
- UEFI Secure Boot
- Trusted Platform Module (TPM)
- I/O MMU virtualization

<!--
IOMMU is a component in a memory controller that translates device virtual addresses into physical addresses.

The IOMMU’s DMA re-mapping functionality is necessary in order for VMDirectPath I/O to work. DMA transactions sent by the passthrough PCI function carry guest OS physical addresses which must be translated into host physical addresses by the IOMMU.

Hardware-assisted I/O MMU virtualization called Intel Virtualization Technology for Directed I/O (VT-d) in Intel processors and AMD I/O Virtualization (AMD-Vi or IOMMU) in AMD processors, is an I/O memory management feature that remaps I/O DMA transfers and device interrupts. This feature (strictly speaking, is a function of the chipset, rather than the CPU) can allow virtual machines to have direct access to hardware I/O devices, such as network cards, storage controllers (HBAs), and GPUs.
-->

The contents of the Vagrant box is documented in https://www.vagrantup.com/docs/providers/vmware/boxes#contents

> The files that are strictly required for a VMware machine to function are: nvram, vmsd, vmx, vmxf, and vmdk files.
>
> There is also the "metadata.json" file used by Vagrant itself.

Once we have created a suitable virtual machine in VMware Workstation we have:

```
blank-disk1.vmdk
blank-disk2.vmdk
blank-disk3.vmdk
blank-disk4.vmdk
blank.vmsd
blank.vmx
blank.vmxf
blank.vmx.lck
```

Notably missing is the nvram file but it seems that we do without it. We will also skip the lck directory.

<!--
Guide for Workstation 16.x

Choose to create a new VM.

Guest Operating System Installation
- Choose: I will install the operating system later

Select Guest Operating System
- Select: Linux
- Version: Other Linux 3.x kernel 64-bit

Name the Virtual Machine
Name: blank

Processor Configuration
Number of processors: 1
Number of cores per processor: 2

Memory for the Virtual Machine
Memory: 1024 MB

Network Type
Network Connection: NAT

Select I/O Controller Types
SCSI Controller: LSI Logic (non SAS)

Select a Disk Type
Virtual Disk Type: SCSI

Select a Disk
Disk: Create a new virtual disk

Specify Disk Capacity
Disk Size: 10 GB
Allocate all disk space now: No
Choose: Store virtual disk as a single file

Specify Disk File
File name: blank-disk1.vmdk

Click on Customize Hardware
Remove the "CD/DVD" and "Network adapter"

Click Add
Choose "CD/DVD Drive"
Uncheck "Connect at power on"
Repeat to have 2x CD/DVD drives

Click Add
Choose "Network Adapter"
Check "Connect at power on"
Select NAT
Repeat to have 4x network adapters

Finish the wizard

Edit virtual machine settings

Click Add
Choose "Hard Disk"
Select SCSI
Select a Disk
Disk: Create a new virtual disk
Specify Disk Capacity
Disk Size: 10 GB
Allocate all disk space now: No
Choose: Store virtual disk as a single file
Specify Disk File
File name: blank-diskN.vmdk (where N is 2, then 3, then 4)
Repeat to have 4x hard disks

Click Save

Edit virtual machine settings

Click Options
Click Advanced
Check "Enable Template Mode"
Firmware type
Select UEFI

Click Save
-->
