# SSH Key Pairs

If you’re managing Linux systems for real, you’ll live and die by SSH keys. Passwords are for tourists. But poorly handled keys are a security mess waiting to happen.

---

## Creating a Key Pair

On your workstation:

    ssh-keygen -t ed25519 -C "your.email@example.com"

This creates:

- `~/.ssh/id_ed25519` (private key — guard this with your life)  
- `~/.ssh/id_ed25519.pub` (public key — safe to share)

*Pro Tip*: Always use a passphrase. A stolen private key without one is just a password that never expires.

---

## Copying the Key to a Server

Use:

    ssh-copy-id user@host

or manually append your `.pub` key to:

    ~/.ssh/authorized_keys

on the remote server.

Permissions matter:

    chmod 700 ~/.ssh
    chmod 600 ~/.ssh/authorized_keys

---

## Testing

    ssh -i ~/.ssh/id_ed25519 user@host

If you get in without a password prompt — success.

---

## Managing Multiple Keys

In `~/.ssh/config`:

    Host webserver
        HostName webserver.example.com
        User admin
        IdentityFile ~/.ssh/id_ed25519

This avoids typing full commands every time.

*Pro Tip*: Name your keys by purpose — `id_backup`, `id_ansible`, etc. — instead of reusing one key everywhere.

---

## Rotating Keys

Keys should be rotated regularly, especially for shared environments.  
Keep a change window and test before deleting the old key.

---

## Troubleshooting

- Wrong permissions → SSH refuses to use insecure files.  
- Wrong user home dir or SELinux labels → Fix with:

      restorecon -Rv ~/.ssh

- Verbose mode helps:

      ssh -vvv user@host

---