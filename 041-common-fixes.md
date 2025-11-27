# SELinux: Common Fixes

Most SELinux issues boil down to file labels or missing booleans. Instead of turning it off, use the tools that fix things properly.

---

## Fixing File Contexts

If files have the wrong labels (common after restores or manual moves):

    restorecon -Rv /var/www/html

Check labels:

    ls -Z /var/www/html

*Pro Tip*: If a file or dir *never* had the right context, add a permanent rule:

    semanage fcontext -a -t httpd_sys_content_t "/srv/www(/.*)?"
    restorecon -Rv /srv/www

---

## Service Access

Some services need extra permissions. Common ones:

- Allow Apache to connect out:

      setsebool -P httpd_can_network_connect 1

- Allow NFS home directories:

      setsebool -P use_nfs_home_dirs 1

- Allow FTP with home dirs:

      setsebool -P ftp_home_dir 1

*Pro Tip*: Use `getsebool -a | grep httpd` to discover available booleans.

---

## Writing Custom Modules

If no existing boolean or context fix works:

    ausearch -m avc -ts recent
    audit2allow -a -M mypolicy
    semodule -i mypolicy.pp

This generates a minimal SELinux policy module to allow what was blocked.

---

## Debugging Tips

- Always confirm the denial in audit logs.  
- Don’t blindly copy-paste audit2allow output — review what it’s permitting.  
- Keep custom policies versioned (git, Ansible, etc.).

---

## When to Escalate

If you’re spending hours writing custom rules, check if your RHEL version has policy updates. Sometimes it’s just a bug that’s already been patched.

---