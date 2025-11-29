# Snapshot Logic

Snapshots are not backups — but they *do* give you a safe, consistent point-in-time view of data while your backup tool (rsync, tar, database dump, whatever) works.  
Used right, snapshots make backups clean.  
Used wrong, they fill up your VG and take the filesystem with them.

---

## What a Snapshot Actually Is

An LVM snapshot is a “copy-on-write” view:

- The original LV keeps running
- The snapshot stores changes made *after* creation
- If the snapshot runs out of space, it’s invalidated (and your backup is trash)

*Pro Tip*: Always size snapshots for the expected churn (writes) during backup, not the full LV size.

---

## Creating a Snapshot

Say `/data` lives on `vgdata/lvdata`:

    lvcreate -s -L 10G -n snap-data /dev/vgdata/lvdata

Check:

    lvs

Mount it:

    mkdir -p /mnt/snap
    mount /dev/vgdata/snap-data /mnt/snap

Now you have a frozen view of the data at the moment the snapshot was taken.

---

## Running a Backup From the Snapshot

Backup from `/mnt/snap` instead of the live filesystem:

    rsync -avh /mnt/snap/ backup:/data/

This gives you consistency even while apps continue writing to the live LV.

---

## Removing the Snapshot (Important)

When you're done:

    umount /mnt/snap
    lvremove /dev/vgdata/snap-data

If you forget this step, the snapshot will keep consuming space until it pops.

---

## Sizing the Snapshot

A rule that works in practice:

- Small databases → 1–5% of LV size  
- Busy filesystems → 10–20%  
- Heavy churn (CI systems, high-write apps) → 20–50%

If unsure, watch snapshot usage:

    lvs

Look at “Data%” and “Metadata%”.

If it approaches 100%, the snapshot is about to break.

---

## Automating Snapshot Backups

Snapshot script example:

```bash
#!/bin/bash
set -e

# Create snapshot
lvcreate -s -L 10G -n snap-data vgdata/lvdata

# Mount snapshot
mount /dev/vgdata/snap-data /mnt/snap

# Run backup
rsync -avh /mnt/snap/ backup:/data/

# Clean up
umount /mnt/snap
lvremove -f vgdata/snap-data





Here’s **`131-snapshot-logic.md`** — the practical way to use snapshots without blowing up your disks or fooling yourself into thinking they’re a full backup:

````bash
# Snapshot Logic

Snapshots are not backups — but they *do* give you a safe, consistent point-in-time view of data while your backup tool (rsync, tar, database dump, whatever) works.  
Used right, snapshots make backups clean.  
Used wrong, they fill up your VG and take the filesystem with them.

---

## What a Snapshot Actually Is

An LVM snapshot is a “copy-on-write” view:

- The original LV keeps running
- The snapshot stores changes made *after* creation
- If the snapshot runs out of space, it’s invalidated (and your backup is trash)

*Pro Tip*: Always size snapshots for the expected churn (writes) during backup, not the full LV size.

---

## Creating a Snapshot

Say `/data` lives on `vgdata/lvdata`:

    lvcreate -s -L 10G -n snap-data /dev/vgdata/lvdata

Check:

    lvs

Mount it:

    mkdir -p /mnt/snap
    mount /dev/vgdata/snap-data /mnt/snap

Now you have a frozen view of the data at the moment the snapshot was taken.

---

## Running a Backup From the Snapshot

Backup from `/mnt/snap` instead of the live filesystem:

    rsync -avh /mnt/snap/ backup:/data/

This gives you consistency even while apps continue writing to the live LV.

---

## Removing the Snapshot (Important)

When you're done:

    umount /mnt/snap
    lvremove /dev/vgdata/snap-data

If you forget this step, the snapshot will keep consuming space until it pops.

---

## Sizing the Snapshot

A rule that works in practice:

- Small databases → 1–5% of LV size  
- Busy filesystems → 10–20%  
- Heavy churn (CI systems, high-write apps) → 20–50%

If unsure, watch snapshot usage:

    lvs

Look at “Data%” and “Metadata%”.

If it approaches 100%, the snapshot is about to break.

---

## Automating Snapshot Backups

Snapshot script example:

```bash
#!/bin/bash
set -e

# Create snapshot
lvcreate -s -L 10G -n snap-data vgdata/lvdata

# Mount snapshot
mount /dev/vgdata/snap-data /mnt/snap

# Run backup
rsync -avh /mnt/snap/ backup:/data/

# Clean up
umount /mnt/snap
lvremove -f vgdata/snap-data
````

Put this under cron or a systemd timer.

---

## When Not to Use Snapshots

* When the filesystem is nearly full
* When LVM free space is low
* When the workload has unpredictable write bursts
* When backing up extremely large, rapidly-changing databases (use DB-native tools instead)

Snapshots fail *hard* if they run out of space.

---

## Other Snapshot Types

* **XFS has no native snapshots** — must use LVM or external tools
* **Btrfs** and **ZFS** have excellent native snapshots
* **VM snapshots** (hypervisor-level) are not a substitute for filesystem consistency

---

## The Real Point

Snapshots aren’t backups — they’re a staging tool.

They give you:

* A consistent point in time
* Minimal downtime
* Safety from app-level writes during backup
* A simpler recovery path

But if they’re your only copy of data, you don’t have a backup. You have a fragile moment in time.

---
