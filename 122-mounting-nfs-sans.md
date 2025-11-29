# Mounting NFS/SANs

Network storage behaves differently from local disks.  
NFS, iSCSI, Fibre Channel, and SAN-backed volumes all have extra requirements, extra failure modes, and extra places to misconfigure things.  
This chapter keeps it simple and reliable.

---

# NFS Mounts

## Basic NFS Mount

    mount -t nfs server:/exports/data /data

Add it permanently using fstab:

    server:/exports/data  /data  nfs  defaults,_netdev  0  0

*Pro Tip*: `_netdev` prevents the system from trying to mount NFS before networking is ready.

---

## Recommended Options

For most environments:

    nfs4  rsize=1048576,wsize=1048576,hard,intr,noatime,_netdev

Meaning:

- `hard` → don’t give up on the server  
- `intr` → allow interrupts (client can stop stuck processes)  
- `noatime` → reduces metadata writes  
- `rsize/wsize` → jumbo read/write sizes for speed  

---

## Automounting with Systemd (Preferred)

NFS can hang at boot. Use automount:

`/etc/systemd/system/data.mount`:

    [Mount]
    What=server:/exports/data
    Where=/data
    Type=nfs
    Options=defaults,_netdev

`/etc/systemd/system/data.automount`:

    [Automount]
    Where=/data

Enable:

    systemctl daemon-reload
    systemctl enable --now data.automount

This prevents long boot delays and NFS lockups.

---

## Checking NFS Health

    showmount -e server
    mount | grep nfs
    nfsstat -s
    nfsstat -c

Monitor logs:

    journalctl -u nfs-client.target

---

# SAN / iSCSI / Fibre Channel

## Discovering iSCSI Targets

    iscsiadm -m discovery -t sendtargets -p server

Log in:

    iscsiadm -m node -T targetname -p server -l

List sessions:

    iscsiadm -m session

---

## Multipathing (Important)

Enterprise SANs require multipath:

Install tools:

    dnf install device-mapper-multipath

Enable:

    mpathconf --enable --with_multipathd y

Check:

    multipath -ll

Your SAN LUNs should appear as `/dev/mapper/mpath*`.

---

## Creating LVM on SAN LUNs

Treat SAN-backed devices as normal disks:

    pvcreate /dev/mapper/mpatha
    vgcreate vgdata /dev/mapper/mpatha
    lvcreate -L 100G -n lvdata vgdata

Then mount as usual.

---

## Timeouts & Recovery

NFS or SAN outages can freeze processes.  
Use safer mount options:

- `soft` (less strict, but can cause data corruption — use only for read-only mounts)  
- `hard,intr` (better for most workloads)  
- `x-systemd.automount` to prevent blocking

---

## Testing Connectivity

    ping server
    rpcinfo -p server
    telnet server 2049   (NFS)
    iscsiadm -m session

---

## When Things Break

- Wrong network/VLAN  
- Firewalls blocking 2049 (NFS) or iSCSI ports  
- Missing multipath config  
- Stale file handles (`ESTALE` errors)  
- Hung processes due to `hard` NFS mounts without interrupts  

---
