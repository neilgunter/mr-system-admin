# Checklist-Based Hardening

Hardening doesn’t have to be a giant policy document. In real environments, you want a short, brutal checklist you can run through whenever building or auditing a server.  
This is that checklist.

---

## Accounts & Access

- Disable root SSH login:

      PermitRootLogin no

- Enforce key-based SSH:

      PasswordAuthentication no

- Confirm user shells:

      getent passwd | grep -E "nologin|false"

- Lock unused system accounts:

      usermod -s /sbin/nologin accountname

- Ensure sudo is required (no direct root use).  
- Put SSH access under an `AllowGroups` rule (see earlier chapter).

---

## SSH Configuration

Edit `/etc/ssh/sshd_config`:

- Protocol 2 only  
- AllowGroups for access control  
- Disable X11 forwarding unless required  
- Use strong host keys (ed25519 preferred)

Reload:

    systemctl restart sshd

*Pro Tip*: After changing SSH settings, test a second session before closing your current one.

---

## Filesystem & Mount Settings

- Add `nodev`, `nosuid`, `noexec` where appropriate:

      /tmp
      /var/tmp
      /home

Using either fstab or mount units.

- Ensure `tmpfs` is mounted on `/run` and `/dev/shm`.

Check:

    mount | grep tmpfs

---

## SELinux

- Confirm SELinux is enforcing:

      getenforce

- Fix labels after file restores:

      restorecon -Rv /path

- Check for denials:

      ausearch -m avc -ts recent

SELinux stays on unless there’s a compelling reason — and “annoying” isn’t one.

---

## Firewalld

- Enable and start:

      systemctl enable --now firewalld

- Set default zone:

      firewall-cmd --set-default-zone=public

- Permit only required services or ports:

      firewall-cmd --permanent --add-service=ssh
      firewall-cmd --permanent --add-port=443/tcp

- Reload:

      firewall-cmd --reload

---

## Packages & Updates

- Disable and remove unused services:

      systemctl disable --now service

- Keep system updated:

      dnf update

- Consider version locks on critical packages.

---

## Logging & Audit

- Ensure persistent journald:

      ls -ld /var/log/journal || mkdir -p /var/log/journal

- Enable auditd:

      systemctl enable --now auditd

- Review:

      aureport

---

## Network

- Disable IPv6 if environment requires it.  
- Harden sysctl:

      net.ipv4.conf.all.rp_filter = 1
      net.ipv4.tcp_syncookies = 1

Apply:

      sysctl -p

---

## Quick Validation Script (Optional)

Use this as a jumpstart for automation:

    #!/bin/bash
    getenforce
    systemctl is-enabled firewalld
    firewall-cmd --list-all
    ss -tuln
    ausearch -m avc -ts recent
    grep -E "^PermitRootLogin|^PasswordAuthentication" /etc/ssh/sshd_config

---