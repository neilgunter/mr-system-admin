# Reboot Strategies

Reboots are simple until they aren’t. On production systems, a reboot is an outage, a gamble, or a scheduled ritual everyone hopes will be boring. This chapter keeps reboots predictable and safe.

---

## Know What You’re Rebooting Into

Check the default kernel:

    grubby --default-kernel

List all kernels:

    rpm -qa | grep kernel

If you’ve pinned a kernel or installed a new one, confirm you’re booting the right version *before* restarting.

---

## Check for Failed Units First

    systemctl --failed

If anything essential is broken before reboot, it’ll still be broken afterward — except now you have fewer tools available during early boot.

---

## Verify Services Enablement

    systemctl list-unit-files | grep enabled

Critical services (sshd, networking, storage units, automounts) should be enabled and clean.

---

## Live View Before Restart

If you suspect problems:

    journalctl -p err.. -b

Look for:
- disk errors  
- mount failures  
- SELinux denials  
- networking flaps  

Fix them before rebooting.

---

## Notify Users (If Applicable)

Production etiquette:
- notify  
- schedule  
- document  
- verify windows  

A reboot without warning is how outages get remembered.

---

## Safe Reboot Command

    systemctl reboot

Alternatively, a clean shutdown:

    systemctl poweroff

Avoid the old:

    reboot
    shutdown -r now

They work, but systemd-native commands behave more consistently.

---

## Reboot With a Specific Kernel

    grubby --set-default /boot/vmlinuz-<version>
    systemctl reboot

This is essential after kernel pinning, rollbacks, or regression testing.

---

## Confirm Services After Boot

After the system comes back:

    systemctl --failed
    systemctl status network
    systemctl status sshd
    systemctl status app.service

Then:

    journalctl -b | grep -i error

This confirms nothing broke during early boot.

---

## Checking Boot Time

    systemd-analyze
    systemd-analyze blame

Useful when a reboot suddenly starts taking minutes instead of seconds.

---

## Staggered Reboots (Fleet Work)

If you’re rebooting multiple servers:

1. Reboot a non-critical node  
2. Validate  
3. Reboot secondary nodes  
4. Reboot primaries or load-bearing machines last

Never reboot an entire tier at once unless you enjoy chaos.

---

## When You Should Avoid Rebooting

- When disk health is questionable  
- When fstab has untested changes  
- When network configs were edited but not validated  
- When SELinux was toggled without verifying contexts  
- During peak load unless absolutely required  

Reboot only when the system’s state is predictable.

---

## When You *Should* Reboot

- Kernel updates  
- Glibc/security updates  
- Broken long-running daemons  
- Hung storage after failover  
- Memory leaks or runaway processes  
- After fixing a systemd unit that can’t reload

Reboots are a tool — use them deliberately.

---
