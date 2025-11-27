# journalctl Like a Boss

If something breaks on a Linux system, the truth is almost always sitting in the logs. `journalctl` is the key to reading it without going cross-eyed.

---

## Basic Usage

    journalctl

That dumps the entire system log. Too much? Of course it is.

Filter by time:

    journalctl --since "10 minutes ago"
    journalctl --since yesterday

Filter by unit:

    journalctl -u sshd
    journalctl -u httpd

*Pro Tip*: Always start with the unit. Saves you scrolling through unrelated noise.

---

## Watching Logs Live

    journalctl -u app.service -f

This is your real-time tail.

---

## Boot Logs

See what happened during boot:

    journalctl -b

Previous boot:

    journalctl -b -1

Perfect when someone swears “it was working before the reboot.”

---

## Errors Only

    journalctl -p err..

Or the long form:

    journalctl -p 3

Priorities range from `0` (emerg) to `7` (debug).

---

## Grep Without Grep

    journalctl | grep something

Yes, it works, but you can filter internally:

    journalctl MESSAGE_ID=some-id
    journalctl SYSLOG_IDENTIFIER=sshd

---

## Logs for a Specific PID

    journalctl _PID=1234

Ideal when a misbehaving process is spamming something cryptic.

---

## Persistent Logs

If logs disappear after reboot, persistence may be off. Check:

    ls -ld /var/log/journal

If it doesn’t exist:

    mkdir -p /var/log/journal
    systemd-tmpfiles --create --prefix /var/log/journal

---