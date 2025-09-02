# When Yum Dies

Even though `yum` has been replaced by `dnf`, you’ll still see it everywhere. And sometimes, it just… stops working. Broken repos, locked databases, half-installed packages — welcome to sysadmin life.

---

## First Check: Is It Really Dead?

Run:

    dnf clean all
    dnf makecache

Sometimes it’s just stale metadata.

*Pro Tip*: If one repo is down, disable it temporarily:

    dnf --disablerepo=bad-repo install package

---

## Database Locks

If you see “Another app is currently holding the dnf lock”:

    ps aux | grep dnf
    kill -9 <pid>

Then try again. Usually caused by an aborted update or another package tool running.

---

## Transaction Problems

If installs/upgrades fail:

    dnf history
    dnf history undo <ID>
    dnf history rollback <ID>

These can rewind you to a stable state.

---

## Dependency Hell

When `dnf` refuses to resolve dependencies:

    dnf deplist package
    dnf repoquery --requires package

This shows you what’s missing and from where.

*Pro Tip*: Sometimes enabling *all* repos helps. Other times, you need to pull in a specific RPM manually.

---

## When All Else Fails

- Rebuild the RPM database:

      rpm --rebuilddb

- Force reinstall a package:

      dnf reinstall package

- Download and install an RPM manually:

      rpm -ivh package.rpm

---

## Emergency Kit

Keep these handy:

- `dnf download package` (grab RPMs directly)  
- `dnf swap` (replace packages cleanly)  
- `rpm -e --nodeps` (last resort, can break things)  

---