# Disabling Root SSH

Direct root SSH access is a bad habit that still lurks in older environments. Disabling it is one of the fastest wins in hardening a server — as long as you don’t cut the branch you’re sitting on.

---

## Before You Touch Anything

Make sure you have:

1. A working **non-root** user  
2. That user is in the sudo group (usually `wheel`)  
3. You have tested sudo with:

       sudo -l
       sudo whoami

If that prints `root`, you’re good.

*Pro Tip*: Always keep your current SSH session open while testing. Use a second shell for experiments.

---

## Disable Root Login

Edit:

    /etc/ssh/sshd_config

Find or add:

    PermitRootLogin no

Optional but recommended:

    PasswordAuthentication no

Reload SSH:

    systemctl restart sshd

---

## Testing the Change

Open a **new session**:

    ssh root@host

It should refuse.  
Then test:

    ssh admin@host
    sudo whoami

If both work, you’ve done it correctly.

---

## Forcing Key-Only Access

To allow root login *only* by key (rare, but some environments do this):

    PermitRootLogin prohibit-password

This allows key access but blocks passwords.

---

## What If Something Breaks?

You can always revert if done carefully:

    sudo vi /etc/ssh/sshd_config
    systemctl restart sshd

Worst case:  
Access the machine through console, IPMI, DRAC, iLO, or hypervisor console.  
Fix the config and restart SSH.

---

## Audit for Stragglers

Check whether root is still allowed somewhere else:

    grep -R PermitRootLogin /etc/ssh*

Look for duplicate configs or included files.

---

## When to Keep Root SSH (Temporarily)

- Fresh installs before users are created  
- Rescue or migration scenarios  
- Appliances or devices with no user accounts

But once the system is stable, root SSH should go away.

---