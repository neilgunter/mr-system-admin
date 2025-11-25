# Copying Keys the Right Way

There are a dozen ways to get your SSH public key onto a server, and most of them work. But only a few work *reliably* without permissions hell or silent failures.

---

## The Right Way: ssh-copy-id

This handles permissions, appends safely, and won’t clobber existing keys:

    ssh-copy-id -i ~/.ssh/id_ed25519.pub user@host

If it succeeds, you’re good. If it fails, you get a clear error.

*Pro Tip*: Use `-i` explicitly if you manage multiple keys. Avoid guessing.

---

## The Manual Way

Sometimes `ssh-copy-id` won’t work (restricted environments, weird shells, locked-down users). Here’s the manual approach:

1. Copy the key contents:

       cat ~/.ssh/id_ed25519.pub

2. On the server:

       mkdir -p ~/.ssh
       chmod 700 ~/.ssh

3. Append the key:

       echo "ssh-ed25519 AAAA...." >> ~/.ssh/authorized_keys
       chmod 600 ~/.ssh/authorized_keys

This fixes the biggest source of failure: *permissions*.

---

## Avoid These Mistakes

- **Using scp to overwrite authorized_keys**  
  You’ll wipe out other admins’ keys. Bad form.

- **Copying the private key**  
  Never do this. If someone asks, walk away.

- **Wrong home directory or shell**  
  Some accounts have `/sbin/nologin` — SSH won’t work no matter what you do.

---

## Verifying Access

    ssh -i ~/.ssh/id_ed25519 user@host

If you still get a password prompt, run in verbose mode:

    ssh -vvv user@host

Look for lines saying the key was rejected — usually permissions or mismatched key types.

---

## SELinux Notes

If SELinux is on (it should be):

    restorecon -Rv ~/.ssh

This resets labels and prevents silent denials.

---