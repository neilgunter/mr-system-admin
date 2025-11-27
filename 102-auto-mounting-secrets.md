# Auto-Mounting Secrets

Some filesystems shouldn’t stay mounted all the time — encrypted volumes, secret stores, credential bundles, or anything holding data you don’t want hanging open for hours.  
Systemd automounts let you mount these only when accessed, and unmount them automatically when idle.

---

## Why Auto-Mount Secrets?

- Limits exposure window  
- Prevents “always-on” sensitive data  
- Reduces blast radius if the machine is compromised  
- Keeps accidental access or scripts from poking at secrets unnecessarily

If you’ve got anything like `/secure`, `/keys`, `/vault`, or `/crypto`, this pattern helps.

---

## Basic Automount Setup

Let’s assume your encrypted or sensitive data lives on `/secure`.

### 1. The mount unit  
`/etc/systemd/system/secure.mount`:

    [Unit]
    Description=Secure Filesystem

    [Mount]
    What=/dev/mapper/secret-lv
    Where=/secure
    Type=ext4
    Options=defaults,noatime

### 2. The automount unit  
`/etc/systemd/system/secure.automount`:

    [Unit]
    Description=Automount Secure Filesystem

    [Automount]
    Where=/secure

    [Install]
    WantedBy=multi-user.target

Enable:

    systemctl daemon-reload
    systemctl enable --now secure.automount

Now `/secure` won’t mount until the first time it’s accessed.

*Pro Tip*: With automounts, boot will never hang waiting on a device that isn’t ready.

---

## Automatic Unmounting (Idle Timeout)

Add timeout to the mount unit:

    [Mount]
    ...
    TimeoutIdleSec=300

After 5 minutes of inactivity, systemd unmounts it automatically.

---

## Encrypting the Backend

If you’re using LUKS:

    cryptsetup luksOpen /dev/sdb secret-lv

You can automate with a keyfile, TPM, or a manual unlock — depends on how paranoid the environment rightfully is.

---

## Permissions Matter

Make sure only the right users/services can access the mount point.

    chown root:security /secure
    chmod 750 /secure

Without tight perms, automounting secrets is just convenience, not security.

---

## Watching for Mount Events

    journalctl -u secure.automount -f

You’ll see:

- When the mount was triggered  
- When it unmounted  
- Any access that caused it to open up

---

## When to Use This Pattern

- Encrypted data stores  
- Private SSH key directories for automation  
- Sensitive app secrets  
- Build systems producing or consuming credentials  
- Anything that only needs to exist *during* work, not 24/7

---