# Permissions Traps

Permissions issues are the silent killers of Linux troubleshooting. Everything looks fine until one tiny bit is wrong — then suddenly nothing works. These are the traps you’ll hit over and over.

---

## The Classic: Wrong Owner

    ls -l /path/to/file

If a service can’t read or write something, ownership is usually the culprit.

Fix it:

    chown user:group /path/to/file

*Pro Tip*: Always check the service user before guessing.  
For example, Apache runs as `apache`, not `root`. Nginx uses `nginx`.  
Systemd units almost never run as the user you think they do.

---

## Groups You Forgot to Add

User can’t read a file?  
Nine times out of ten, they’re just not in the right group.

    id username

Add the missing group:

    usermod -aG groupname username

And remember: group changes require a **new login**.

---

## Directories Need Execute Bit

Everyone remembers read/write.  
Almost nobody remembers execute on directories.

If you can’t `cd` into a directory:

    chmod +x directory

If an app can’t traverse a directory path, even if the target file is readable, this is usually why.

---

## The “777 Fix” That Haunts You Later

Someone eventually suggests:

    chmod -R 777 /path

Don’t do it. Ever.  
It works because it tears all security off the object.  
You’ll spend weeks cleaning up the mess it creates.

---

## Sticky Bit Confusion

The sticky bit (`t`) matters mostly in world-writable directories like `/tmp`.

    chmod +t /shared/dir

Without it, any user can delete files they don’t own.  
With it, users can only delete their own files.

---

## SUID/SGID Misuse

SUID makes a program run as the file owner. SGID uses the group owner.

You’ll see these occasionally:

    -rwsr-xr-x root root /usr/bin/passwd

Don’t set these casually. They can turn tiny mistakes into privilege-escalation bugs.

---

## ACLs Hidden in the Shadows

When everything *looks* right but still fails, check Access Control Lists:

    getfacl file
    setfacl -m u:user:r file

ACLs override normal permissions and can confuse even seasoned admins.

---

## SELinux Layer on Top

Permissions can be correct, ACLs can be correct — but SELinux can still deny access.

Always check contexts:

    ls -Z file

If something looks wrong, repair:

    restorecon -Rv /path

---