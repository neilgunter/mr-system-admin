# Systemd for Human Beings: Services, Timers, Reloads

Systemd can look complicated, but once you know the handful of commands that matter, it becomes one of the cleanest parts of server management. This is the stuff you’ll use every day.

---

## Starting and Stopping Services

    systemctl start app.service
    systemctl stop app.service
    systemctl restart app.service

*Pro Tip*: Always follow a restart with a live-tail:

    journalctl -u app.service -f

That’s where the real failures show up.

---

## Enabling at Boot

    systemctl enable app.service

Disable:

    systemctl disable app.service

Check status:

    systemctl status app.service

---

## Reload vs Restart

If your service supports reloads:

    systemctl reload app.service

This lets you apply config changes without killing active connections.

If reload fails or isn’t supported, use:

    systemctl restart app.service

---

## Checking Dependencies

    systemctl list-dependencies app.service

Good for spotting what must start before your service can.

---

## Viewing the Unit File

    systemctl cat app.service

This shows you the actual unit definition and any drop-ins.

---

## Timers: Cron Without the Pain

List timers:

    systemctl list-timers

See details:

    systemctl cat backup.timer

Start or stop:

    systemctl start backup.timer
    systemctl stop backup.timer

*Pro Tip*: Timers pair with `.service` files. The timer triggers the service — a clean, logical split compared to crontab chaos.

Example timer:

    [Timer]
    OnCalendar=daily
    Persistent=true

---

## Reloading Systemd

Any time you change unit files:

    systemctl daemon-reload

If you forget this, systemd won’t pick up your edits.

---

## Masking vs Disabling

Disable: prevents starting at boot  
Mask: prevents *anything* from starting it

    systemctl mask app.service
    systemctl unmask app.service

Useful when a broken service keeps trying to start itself.

---