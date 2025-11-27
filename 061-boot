# Boot Logs

When a system fails early in startup, the boot logs are where the bodies are buried. `journalctl` has the keys to that kingdom, but most people only scratch the surface.

---

## Current Boot

    journalctl -b

This shows everything that happened since the last reboot began.

*Pro Tip*: Always check the timestamp near the top — you’ll see exactly when the boot started and where it slowed down.

---

## Previous Boot

    journalctl -b -1

And you can go further:

    journalctl -b -2
    journalctl -b -3

Perfect for chasing issues after repeated failed boots.

---

## Filtering Boot Logs

By unit:

    journalctl -b -u network.service

By priority:

    journalctl -b -p err..

If you only want warnings and errors:

    journalctl -b -p 4

---

## Kernel Messages Only

    journalctl -k

Or for the previous boot:

    journalctl -k -b -1

Good for spotting early driver issues, SELinux denials, or anything that blew up before user space existed.

---

## Boot Performance

    systemd-analyze blame

This tells you what slowed the system down.

Also:

    systemd-analyze critical-chain

This shows the startup path — which service waited on what, and why.

---

## When the System Never Fully Booted

If you can only get to a recovery shell:

    journalctl --root=/mnt/sysimage -b -1

Assuming you mounted the root filesystem at `/mnt/sysimage`, this can read the previous boot log even from rescue mode.

---