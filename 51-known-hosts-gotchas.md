# Known Hosts Gotchas

SSH is picky about host identity — and that’s a good thing. But the `known_hosts` file can turn into a landmine when servers get rebuilt, IPs get reassigned, or someone rotates host keys without telling you.

---

## The Classic Error

    @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    @ WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED! @
    @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@

SSH thinks the server at that IP or hostname isn’t the same machine you connected to before.

Sometimes that’s true. Sometimes it’s just a rebuild.

---

## Fix: Remove the Old Entry

Find the offending line:

    ssh-keygen -f ~/.ssh/known_hosts -F hostname

Then delete it:

    ssh-keygen -f ~/.ssh/known_hosts -R hostname

Or manually open the file and remove the line.

*Pro Tip*: Never blindly delete entries in production environments. Always confirm you’re not being MITM’d.

---

## When Hosts Move or Change IPs

If a server gets a new IP but reuses the same hostname, SSH may complain.

Fix:

    ssh-keygen -R old.ip.address
    ssh-keygen -R hostname

Then reconnect — SSH will ask you to trust the new fingerprint.

---

## Host Key Rotation

Some environments rotate SSH host keys regularly.  
You’ll see errors even if nothing is wrong.

Check the new fingerprint with the system owner before accepting it:

    ssh-keyscan hostname

Compare it with what they give you.

---

## Known Hosts Bloat

Over time, the file becomes a graveyard of old fingerprints.

You can safely remove entries for servers you’ll never connect to again.

---

## Troubleshooting

Verbose mode is your friend:

    ssh -vvv user@host

Look for lines about host key mismatch, fingerprint types, and file locations.

---