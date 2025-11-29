# LVM Basics

LVM looks complicated until you understand the stack. After that, it becomes one of the most flexible tools you have on a Linux server.  
Think of it like this:

**Disk → PV → VG → LV → Filesystem**

That’s the whole game.

---

## The Layers (In Plain English)

### Physical Volume (PV)
A disk or partition you hand over to LVM.

    pvcreate /dev/sdb

### Volume Group (VG)
A pool of storage made from one or more PVs.

    vgcreate vgdata /dev/sdb

### Logical Volume (LV)
A “virtual partition” carved out of the VG.

    lvcreate -L 20G -n lvdata vgdata

### Filesystem
What you actually mount and use.

    mkfs.xfs /dev/vgdata/lvdata
    mount /dev/vgdata/lvdata /data

*Pro Tip*: If you know these four layers, you can troubleshoot almost anything LVM throws at you.

---

## Inspecting LVM at a Glance

    lsblk
    pvs
    vgs
    lvs

These four commands give you the full picture in seconds.

---

## Adding Storage to a VG

1. Add a new disk (`/dev/sdc`)  
2. Make it a PV:

       pvcreate /dev/sdc

3. Extend the VG:

       vgextend vgdata /dev/sdc

Now the VG has more free space.

---

## Extending an LV

Extend by size:

    lvextend -L +10G /dev/vgdata/lvdata

Extend using all free space:

    lvextend -l +100%FREE /dev/vgdata/lvdata

Grow the filesystem:

- XFS:

      xfs_growfs /data

- ext4:

      resize2fs /dev/vgdata/lvdata

If you include `-r`:

    lvextend -r -l +100%FREE /dev/vgdata/lvdata

…it grows the filesystem automatically.

---

## Shrinking (Be Careful)

Shrinking LVs is risky.  
XFS cannot shrink at all.  
Ext4 can shrink, but only offline.

Unless you absolutely have to, avoid shrinking volumes and resize at the filesystem layer first.

---

## Snapshots

LVM snapshots create a point-in-time copy of an LV:

    lvcreate -s -n snap-data -L 5G /dev/vgdata/lvdata

Simple, but remember snapshots grow as changes accumulate.  
If the snapshot fills, it breaks.

---

## Mirroring / RAID

LVM can do RAID:

    lvcreate --type raid1 -m1 -L 20G -n lvdata vgdata

Useful for simple redundancy without hardware RAID.

---

## Why Use LVM?

- Online resizing  
- Snapshots  
- Combine multiple disks  
- Clean storage management  
- Makes VM expansion painless  

If your server isn't using LVM, you're leaving convenience on the table.

---
