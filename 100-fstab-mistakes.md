# fstab Mistakes

`/etc/fstab` is the quiet little file that decides whether your system boots cleanly or drops you straight into emergency mode. One wrong option, one missing mount point, one bad UUID — and you’re in for a long morning.

---

## The Golden Rule

**If a mount isn’t critical, make it non-fatal.**

Use:

    nofail

This keeps the system booting even if the device or NFS path isn’t available.

---

## The “It Worked Yesterday” Problem

If someone changed disks, LUNs, or partitions, the UUIDs probably changed too.

Check UUIDs:

    blkid

Match them against `/etc/fstab`.  
If they don’t match, fix them.

*Pro Tip*: Never assume device names like `/dev/sdb1` are stable. Always prefer UUID or LABEL.

---

## Wrong Mount Options

Common culprits:

- `defaults` assumed to include things it doesn't  
- `auto` vs `noauto` confusion  
- Missing `_netdev` on network mounts  
- Filesystems needing specific options (e.g., XFS ignoring certain ext4-style flags)

Fixing options usually involves reading:

    man 5 fstab

Not fun, but necessary.

---

## Mount Point Doesn’t Exist

This one gets everyone at least once.

If `/data` doesn’t exist:

    mkdir -p /data

Systemd will fail the mount immediately if the directory is missing.

---

## Testing Before Reboot

Never make an fstab change and immediately reboot.  
Test with:

    mount -a

If it fails here, it’ll definitely fail during boot.

---

## NFS Needs `_netdev`

For NFS or any network filesystem:

    _netdev

This tells systemd not to mount it until the network is up.

Also consider:

    nofail
    x-systemd.automount

---

## Emergency Mode After Boot

If you break fstab badly enough, you’ll land here:

    Welcome to emergency mode!

From there:

    mount -o remount,rw /
    vi /etc/fstab
    mount -a

Fix the line, write/quit, then reboot cleanly.

---

## Quick Sanity Checklist

- UUIDs correct?  
- Mount points exist?  
- Options valid for the filesystem?  
- Network mounts use `_netdev`?  
- Non-critical mounts use `nofail`?  
- `mount -a` passes without errors?

---