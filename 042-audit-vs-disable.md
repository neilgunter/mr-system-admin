# SELinux: Audit vs Disable

Every sysadmin eventually asks: “Do I really need SELinux on?” The answer is yes — but there are times when flipping it into permissive mode makes sense. Just don’t leave it off permanently.

---

## Audit Mode (Permissive)

Switch temporarily:

    setenforce 0

Reproduce the problem. SELinux will log denials but won’t block.  

Check the logs:

    ausearch -m avc -ts recent
    sealert -a /var/log/audit/audit.log

If your app works now and AVCs appear, SELinux was the blocker.

*Pro Tip*: Always test in permissive before writing off SELinux entirely.

---

## Disabling (Last Resort)

To disable completely, edit:

    /etc/selinux/config

Set:

    SELINUX=disabled

This requires a reboot. Only do this if:

- You’re in a temporary lab/test environment  
- You need to confirm SELinux is *not* the issue  
- You plan to turn it back on once root cause is clear

---

## Risks of Leaving It Off

- Services run without confinement → one exploit = total compromise  
- Security audits will flag it instantly  
- Future fixes harder, because policies won’t be tested

---

## Safer Alternatives

- Use permissive instead of disabled  
- Write a local policy module  
- File a bug with Red Hat if it’s a policy gap

---

## The Rule of Thumb

- *Permissive* → for diagnosis and debugging  
- *Disabled* → only if you enjoy explaining to auditors why the server failed compliance

---