# DNF Tips

If you’re in RHEL land, `dnf` is your daily bread. It replaces `yum`, but you’ll still hear people say “yum install” because muscle memory dies hard. These are the tricks that make it less painful.

---

## Basic Usage

    dnf install package
    dnf remove package
    dnf search keyword

*Pro Tip*: Always use `dnf info package` before installing. It shows version, repo, and dependencies so you don’t get surprises.

---

## Updating Safely

    dnf update
    dnf upgrade

- `update` → Updates installed packages only.  
- `upgrade` → May remove obsolete packages.  

*Pro Tip*: On production systems, avoid blind `dnf upgrade`. Use version locks or update specific packages.

---

## Querying

    dnf list installed
    dnf list available
    dnf provides /usr/bin/foo

That last one is pure gold when you don’t know which package owns a file.

---

## Caching

    dnf makecache

Useful if your repos are slow.  

Clear cache:

    dnf clean all

---

## Reverting

Yes, you can roll back:

    dnf history
    dnf history undo <ID>

*Pro Tip*: Always check `dnf history list` after updates. It’s your quick exit if something breaks.

---

## Speeding Things Up

- Enable fastest mirror:

    echo "fastestmirror=True" >> /etc/dnf/dnf.conf

- Parallel downloads (RHEL 8+):

    max_parallel_downloads=10

---
