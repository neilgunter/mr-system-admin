# SSH Access by Group

Managing SSH access one user at a time doesn’t scale. The clean way is to control login by *groups*, letting you add or remove access with a single `usermod`.

This is one of the first things you set up when hardening a new environment.

---

## Locking SSH Down to a Group

Edit:

    /etc/ssh/sshd_config

Add:

    AllowGroups sshusers

Restart:

    systemctl restart sshd

Now only users in the `sshusers` group can log in.

Add a user:

    usermod -aG sshusers username

Remove a user (instantly revokes SSH access):

    gpasswd -d username sshusers

*Pro Tip*: Use purpose-driven groups — `sshadmins`, `sshreadonly`, etc. — instead of one giant access bucket.

---

## Multiple Groups Allowed

You can list more than one:

    AllowGroups sshadmins sshdevs sshops

SSH accepts any match.

---

## What About DenyUsers / DenyGroups?

They still work, but AllowGroups is usually cleaner.  
Deny rules execute *after* allow rules, which can cause unnecessary confusion.

---

## Per-User Rules (When Needed)

If you must restrict a specific account further:

    Match User deploy
        PasswordAuthentication no
        X11Forwarding no

This lives at the bottom of `sshd_config`.

---

## SELinux Notes

If you manage SSH contexts:

    ls -Z ~/.ssh
    restorecon -Rv ~/.ssh

Incorrect labels can silently block key-based login.

---

## Troubleshooting

See why access was blocked:

    journalctl -u sshd -f

Look for messages like:

    User username not allowed because not in AllowGroups

That tells you exactly what group you missed.

---