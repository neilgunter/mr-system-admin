# Firewalld the Right Way

Firewalld looks complicated until you realize it’s just nftables with training wheels — and honestly, the wheels help.  
Most of the pain comes from people mixing firewalld, raw iptables, and random scripts. Stick to one method, and stick to firewalld.

---

## Enable It First

    systemctl enable --now firewalld

Check:

    firewall-cmd --state

If it says `running`, you’re in business.

---

## Zones: The Part Everyone Ignores

Zones define trust levels.  
Common ones:

- `public` (default)
- `internal`
- `trusted`
- `dmz`

Check your active zone:

    firewall-cmd --get-active-zones

Find which interface is in which zone:

    firewall-cmd --get-zone-of-interface=eth0

*Pro Tip*: Don’t throw everything in `trusted` unless you enjoy surprises.

---

## Allowing Services the Right Way

Firewalld has service definitions for common daemons:

    firewall-cmd --permanent --add-service=http
    firewall-cmd --permanent --add-service=ssh

Then:

    firewall-cmd --reload

List enabled services:

    firewall-cmd --list-services

This is cleaner than opening ports manually.

---

## Opening Ports Manually

For custom ports:

    firewall-cmd --permanent --add-port=8443/tcp
    firewall-cmd --permanent --add-port=6000-6010/tcp
    firewall-cmd --reload

Check:

    firewall-cmd --list-ports

---

## Change the Zone of an Interface

    firewall-cmd --zone=internal --change-interface=eth0 --permanent
    firewall-cmd --reload

Useful when a server has multiple NICs that shouldn’t share the same trust level.

---

## Rich Rules (Use Sparingly)

If you need more specific rules:

    firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="10.0.0.5" port port="22" protocol="tcp" accept'

Rich rules are powerful, but they complicate audits. Use only when simple rules won’t cut it.

---

## Avoid These Mistakes

- Mixing `iptables` commands with firewalld  
- Forgetting `--permanent` and losing rules on reboot  
- Forgetting `firewall-cmd --reload`  
- Editing `/etc/firewalld/*` directly — let the daemon manage its state  
- Assuming services auto-open ports (most don’t)  

---

## Testing Rules

See packet drops:

    sudo journalctl -u firewalld -f

List everything applied:

    firewall-cmd --list-all

From another host, test connectivity with:

    nc -vz server port

Yes, `nc` is still the fastest way to know if a port is truly open.

---

## If Something Still Doesn’t Work

- SELinux might be blocking the service  
- The service might not be listening on the right interface  
- A higher-level rule could be overriding a lower one  
- Your cloud provider may have its own firewall (AWS, Azure, GCP)

Firewalld only controls the local machine — not upstream networking.

---