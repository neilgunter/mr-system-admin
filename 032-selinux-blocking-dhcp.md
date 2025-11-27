# SELinux Blocking DHCP

Sometimes your server boots, the NIC is up, but you never get an IP via DHCP. Before you blame the network guys, check if SELinux is the troublemaker.

---

## Symptom

- `nmcli device status` shows the interface as “unmanaged” or “connecting.”  
- `journalctl -xe` logs contain AVC denials about `dhclient`.  
- Disabling SELinux makes DHCP suddenly work (not a fix, just a clue).

---

## Quick Diagnosis

Check audit logs:

    ausearch -m avc --comm dhclient
    sealert -a /var/log/audit/audit.log

If you see SELinux blocking `dhclient`, you’ve found your culprit.

---

## Fixes

Enable the right boolean:

    setsebool -P dhcp_lease_write 1

Or relabel if something got messed up:

    restorecon -Rv /var/lib/dhclient/

*Pro Tip*: Never leave SELinux off just to “make DHCP work.” That’s how bad habits spread.

---

## If All Else Fails

- Update SELinux policy packages — older policies sometimes miss DHCP contexts.  
- Check if your NIC config is in `/etc/NetworkManager/system-connections/` with proper SELinux labels.  

---

## The Takeaway

SELinux isn’t always the problem, but when DHCP is mysteriously broken, it’s a suspect worth interrogating.

---