# DNF Version Locks

Sometimes you need a specific package version to *stay* a specific version.  
Maybe an app depends on an older build.  
Maybe a new release breaks something critical.  
Maybe your fleet needs consistency across hundreds of servers.

DNF version locking gives you that control.

---

## Install the Versionlock Plugin

    dnf install python3-dnf-plugins-extras-versionlock

Check it’s available:

    dnf versionlock list

If it returns nothing, that’s fine — it just means no locks yet.

---

## Lock a Package Version

Say you want to pin OpenSSL:

    dnf versionlock add openssl-1:1.1.1k-5.el8

This prevents upgrading *or downgrading* that package.

You can also match wildcards:

    dnf versionlock add openssl*

*Pro Tip*: Always specify the exact version when possible. Wildcards can bite you later.

---

## Lock Whatever Version Is Installed Now

This is the cleanest method when stabilizing an environment:

    dnf versionlock add openssl

This locks the currently installed version, whatever it is.

---

## Viewing Locks

    dnf versionlock list

You’ll see entries like:

    openssl-1:1.1.1k-5.el8.*

---

## Removing Locks

Unlock a package:

    dnf versionlock delete openssl

Or remove them all:

    dnf versionlock clear

---

## Working With Locks During Updates

Normal update:

    dnf update

DNF will skip locked packages and tell you they're locked.

If you **try** to upgrade a locked package:

    dnf update openssl

…it will refuse.  
That’s exactly what you want.

---

## Using Locks in Automation

In Ansible:

```
- name: Lock OpenSSL version
  command: "dnf versionlock add openssl-1:1.1.1k-5.el8"
````

Use locks to pin dependencies for:

* application servers
* database nodes
* legacy apps with brittle requirements
* production fleets needing consistent baselines

---

## Lock Files (Under the Hood)

The lock list lives here:

```
/etc/dnf/plugins/versionlock.list
```

You can manage it manually, though the `dnf versionlock` commands are safer.

---

## When to Use Version Locks

* New updates cause regressions
* Vendor software relies on specific package versions
* You’re maintaining identical golden images
* You need stable build/test pipelines
* You’re preparing for a major OS update and want to freeze deps temporarily

---

## When *Not* to Use Version Locks

* Security-sensitive packages (locking might leave them unpatched)
* Systems where continuous updates are required
* When you’re working around a temporary bug you should really fix upstream

Use locks thoughtfully — they give stability, but they also prevent security fixes.

---
