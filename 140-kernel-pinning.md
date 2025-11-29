# Kernel Pinning

Updating kernels is always a risk. Most of the time it’s fine — until one day it isn’t, and now you’re debugging a broken NIC driver or filesystem regression at 6 AM.  
Kernel pinning gives you control by letting you choose **which** kernel stays active, even as updates install new ones.

---

## Check Current Kernel

    uname -r

List installed kernels:

    rpm -qa | grep kernel

Show bootloader entries:

    grub2-editenv list
    grep menuentry /boot/grub2/grub.cfg

*Pro Tip*: Never pin a kernel unless you’ve confirmed it boots cleanly at least once.

---

## Keeping the Current Kernel During Updates

Edit:

    /etc/dnf/dnf.conf

Add:

    exclude=kernel*

This prevents DNF from installing new kernels at all.

Or inline:

    dnf update --exclude=kernel*

Useful when you want to freeze the kernel version temporarily.

---

## Installing a Specific Kernel Version

    dnf install kernel-<version>

Example:

    dnf install kernel-4.18.0-425.el8

---

## Setting the Default Kernel

Find the index:

    grub2-set-default 0

Or:

    grubby --default-kernel

Set a specific kernel:

    grubby --set-default /boot/vmlinuz-4.18.0-425.el8.x86_64

Confirm:

    grubby --info=ALL

After reboot:

    uname -r

---

## Removing Old Kernels (Safely)

List:

    rpm -qa | grep kernel

Remove extras:

    dnf remove kernel-oldversion

*Pro Tip*: Always keep at least **two** known-good kernels. One for normal use, one for fallback.

---

## Auto-Remove Old Kernels

RHEL keeps a small number by default.  
You can enforce a stricter limit:

    /etc/dnf/dnf.conf
    installonly_limit=3

But don’t set this too low, or you lose your safety net.

---

## When to Pin a Kernel

- Driver issues (NIC, storage controllers, GPU)  
- Regression in a new patch  
- Vendor requirement for a specific kernel  
- Latency-sensitive systems  
- Production clusters where uptime matters more than features  

Pinning buys stability while you test a new kernel elsewhere.

---

## Rolling Back After a Bad Kernel Update

If a new kernel breaks:

1. Boot into the older kernel from GRUB  
2. Set that older kernel as default:

       grubby --set-default /boot/vmlinuz-<oldversion>

3. Clean up the broken one:

       dnf remove kernel-<badversion>

This gives you a stable baseline again.

---

## Real Talk

Kernel updates are low risk… until the day they aren’t.  
Pin judiciously, test carefully, and always — always — keep a fallback kernel installed.

---
