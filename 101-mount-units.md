# Mount Units

Systemd can manage mounts directly using unit files, which gives you better dependency handling, cleaner behavior at boot, and fewer surprises than relying solely on `/etc/fstab`.

If you’ve ever been burned by a bad fstab entry, mount units are the antidote.

---

## Naming Rules

Mount units are named after the mount point:

- `/data` → `data.mount`
- `/var/www/html` → `var-www-html.mount`

Slashes become dashes.  
The file goes in:

    /etc/systemd/system/

---

## Example: Static Mount

`/etc/systemd/system/data.mount`:

    [Unit]
    Description=Data Mount
    After=network.target

    [Mount]
    What=/dev/mapper/vgdata-lvdata
    Where=/data
    Type=xfs
    Options=defaults

    [Install]
    WantedBy=multi-user.target

Enable and start:

    systemctl daemon-reload
    systemctl enable --now data.mount

*Pro Tip*: Mount units auto-handle ordering — you don’t need `_netdev` hacks or guesswork.

---

## Automounting

Useful when you don’t want blocking at boot or when a mount is only occasionally used.

Create a `.automount` file:

`/etc/systemd/system/data.automount`:

    [Unit]
    Description=Automount Data

    [Automount]
    Where=/data

    [Install]
    WantedBy=multi-user.target

Enable it:

    systemctl enable --now data.automount

Systemd will mount `/data` on first access, cleanly and without freezing boot.

---

## Splitting Mount and Automount

You keep both:

- `data.mount` → defines the mount  
- `data.automount` → controls autostart behavior  

This is the reliable way to manage NFS mounts.

---

## Checking Status

    systemctl status data.mount
    systemctl status data.automount

See logs:

    journalctl -u data.mount

---

## When to Use Mount Units

- NFS mounts that must not block boot  
- Environments with flaky storage  
- Systems needing precise dependency control  
- Replacing messy or high-risk `/etc/fstab` entries  

---