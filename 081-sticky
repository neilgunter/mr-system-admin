# Sticky Bit Gotchas

The sticky bit is one of those things everyone vaguely remembers from a certification book, then forgets until it bites them. It matters most in shared directories where many users write files.

---

## What the Sticky Bit Actually Does

When set on a directory:

    chmod +t /shared/dir

Only the file **owner** (or root) can delete or rename files within that directory — even if the directory is world-writable.

This is why `/tmp` works safely.

Check it:

    ls -ld /tmp
    drwxrwxrwt ...

That trailing `t` means “sticky.”

---

## When You Need It

- Shared directories  
- Upload directories for web apps  
- Anything world-writable by design  
- Build/CI systems that produce temp artifacts  

If multiple users write into the same directory, the sticky bit keeps one user from deleting another user’s files.

---

## When You Forget It

A shared directory without sticky bit:

    drwxrwxrw-  nobody  nobody  /uploads

This allows any user to delete any other user’s files.  
You’ll smell this problem when people report “random disappearing files.”

Fix it:

    chmod 1777 /uploads

---

## Common Mistakes

- **Setting sticky bit on files**  
  Doesn’t do what you think. Sticky bit on regular files is obsolete for most systems.

- **Relying on sticky bit for security**  
  It prevents accidental deletion, not malicious tampering.  
  Combine it with the right owner/group/ACLs.

- **Forgetting execute on the directory**  
  Without execute (`x`), users can’t enter the directory at all — sticky bit or not.

---

## Quick Checks

To list directories with sticky bit:

    find / -type d -perm -1000 2>/dev/null

Useful in audits or when debugging shared filesystem weirdness.

---