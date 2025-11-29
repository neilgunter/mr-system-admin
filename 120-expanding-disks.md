# Expanding Disks

Sooner or later, a filesystem fills up at the worst possible time. Expanding storage on RHEL isn’t hard — as long as you know which layer you’re working with: VM disk → partition → PV → VG → LV → filesystem.  
Miss one step, and nothing grows.

---

## Step 1: Confirm the Disk Is Actually Bigger

If it’s a VM and someone expanded the virtual disk, verify the OS sees the change:

    lsblk
    fdisk -l

If the size hasn’t changed, rescan:

    echo 1 > /sys/class/block/sda/device/rescan

or for SCSI:

    rescan-scsi-bus

*Pro Tip*: Don’t continue until the OS sees the new size. Everything else depends on it.

---

## Step 2: Extend the Partition (If Using One)

For systems with a single partition (`/dev/sda2`):

    growpart /dev/sda 2

or manually with `fdisk`/`parted`, but `growpart` is safer and faster.

Check:

    lsblk

The partition should now reflect the new size.

---

## Step 3: Extend the Physical Volume (PV)

If using LVM:

    pvresize /dev/sda2

Check free space:

    pvs
    vgs

PV should now show unallocated space inside the VG.

---

## Step 4: Extend the Logical Volume (LV)

Find your LV:

    lvdisplay
    lvs

Extend:

    lvextend -r -L +10G /dev/mapper/vgdata-lvdata

`-r` grows the filesystem **automatically** (XFS and ext4 both supported).

Or extend by using all free space:

    lvextend -r -l +100%FREE /dev/mapper/vgdata-lvdata

---

## Step 5: Grow the Filesystem (If You Didn’t Use -r)

For XFS:

    xfs_growfs /mountpoint

For ext4:

    resize2fs /dev/mapper/vgdata-lvdata

Check:

    df -h

Your new space should appear.

---

## Special Case: No LVM

If you’re not using LVM (not recommended for servers):

- Extend the partition  
- Grow filesystem directly:

For XFS:

    xfs_growfs /mountpoint

For ext4:

    resize2fs /dev/sda1

*Pro Tip*: This is why LVM matters. Without it, resizing is far more painful.

---

## Expanding Disks in VMs

VMware:

    vmware-toolbox-cmd disk list
    echo 1 > /sys/class/block/sda/device/rescan

KVM/Proxmox:

    qemu-img resize disk.qcow2 +20G

Hyper-V:

    Resize-VHD -Path disk.vhdx -SizeBytes 200GB

Cloud providers:

- AWS: modify volume, wait for optimizing  
- Azure: resize disk in portal  
- GCP: resize persistent disk, then `growpart`

---

## Safety Checklist

Before touching anything:

- Backups exist  
- Snapshots taken (VMs)  
- PV/VG/LV structure understood  
- App owners notified  
- Space needs validated (no “just add more” pattern)

---

## Quick Verification

    df -h
    lsblk
    vgs
    lvs

If all four look correct, the expansion succeeded.

---
