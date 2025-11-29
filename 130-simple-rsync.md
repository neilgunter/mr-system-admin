# Simple rsync Backups

Rsync is the workhorse of Linux backups — simple, fast, battle-tested, and built for incremental transfers.  
If you get rsync right, you already have 80% of a real backup strategy.

---

## The Golden Pattern

A clean, dependable rsync backup looks like this:

    rsync -avh --delete /source/ user@backup:/dest/

Flags explained:

- `-a` → archive mode (preserves perms, symlinks, ownership)
- `-v` → verbose
- `-h` → human-readable
- `--delete` → removes files on the destination that disappeared from the source

*Pro Tip*: Do not add `--delete` until you're absolutely sure your backup tree is correct.

---

## Backup Over SSH (Most Common)

Set up SSH keys for passwordless access, then:

    rsync -avh /var/www/ backupsrv:/backups/www/

The remote host should store backups on a dedicated filesystem or volume.

---

## Excluding Files

Create an exclude file:

`/root/rsync-excludes.txt`:

    *.tmp
    cache/
    node_modules/
    logs/

Then run:

    rsync -avh --exclude-from=/root/rsync-excludes.txt /source/ backup:/dest/

---

## Using a Pull Model

Sometimes the backup server should pull data instead of pushing:

On backup server:

    rsync -avh server:/data/ /backups/server/

This prevents compromised servers from overwriting backup content.

---

## Snapshot-Friendly Structure

Organize backups like:

    /backups/host/YYYY-MM-DD/
    /backups/host/latest -> YYYY-MM-DD/

Rotate old directories, shift the symlink, done.

---

## Cron + Logging

Backup script:

    #!/bin/bash
    rsync -avh --delete /data/ backup:/data/ >> /var/log/backup.log 2>&1

Cron entry:

    0 2 * * * /usr/local/bin/backup.sh

Remember to rotate logs.

---

## Test Restores (Seriously)

Your backup is only as good as your restore process.

Test:

    rsync -avh backup:/data/ /restore-test/

Or mount the backup volume read-only and verify files and services.

---

## When rsync Isn't Enough

- Databases (use logical/physical dumps first)
- Running VMs (use snapshots or hypervisor tools)
- Services that need consistent state (use application-level backups)

Rsync isn't magic — it copies bits. Make sure the bits represent a usable state.

---
