# Overriding Units

You should almost never edit systemd unit files directly in `/usr/lib/systemd/system`. Those belong to the OS or the package. If you change them, updates will stomp your edits and leave you guessing why things broke.

The right way is to create **overrides**.

---

## Show the Current Unit

    systemctl cat app.service

This displays:
- The main unit in `/usr/lib/systemd/system`
- Any overrides in `/etc/systemd/system/app.service.d/`

---

## Create an Override

    systemctl edit app.service

This opens an override file (or creates one if none exists) at:

    /etc/systemd/system/app.service.d/override.conf

Add only what you want to change. Example:

    [Service]
    Environment="APP_MODE=production"
    Restart=on-failure

*Pro Tip*: Overrides only add or modify. They don’t replace the whole file — much safer.

---

## Drop-In Directory Structure

Overrides live here:

    /etc/systemd/system/app.service.d/

You can have multiple files inside it. Systemd merges them in alphabetical order:

    10-env.conf
    20-limits.conf

This makes versioning cleaner.

---

## Applying the Override

After saving:

    systemctl daemon-reload
    systemctl restart app.service

If you forget `daemon-reload`, systemd won’t see your changes.

---

## Removing an Override

    systemctl edit --drop-in=override.conf --full app.service

Or delete the file manually:

    rm /etc/systemd/system/app.service.d/override.conf
    systemctl daemon-reload

---

## Why Overrides Matter

- Survive package updates  
- Easy to track in git or Ansible  
- Only touch the specific settings you intend  
- No surprises from upstream changes  

---