# Logging Cron Output

Cron doesn't email you by default anymore, and most environments don’t have a mail subsystem configured anyway. If you’re not capturing output yourself, cron jobs will fail quietly — and nothing is worse than silent failure.

---

## The Simple Way: Redirect Output

Append to a log:

    0 2 * * * /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1

Overwrite each time:

    0 2 * * * /usr/local/bin/backup.sh > /var/log/backup.log 2>&1

*Pro Tip*: Always capture stderr (`2>&1`). Cron errors almost always come out there.

---

## Use a Wrapper Script

Instead of dumping inline commands into crontab, call a script:

    0 2 * * * /usr/local/bin/backup-wrapper.sh

And inside the wrapper:

    #!/bin/bash
    /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1

Cleaner, easier to debug, and versionable.

---

## Timestamp Your Output

In the wrapper script:

    echo "[$(date)] Starting backup" >> /var/log/backup.log

This makes reading logs less painful.

---

## Using systemd-run for Better Logging

For one-off or ad-hoc jobs:

    systemd-run --unit=mycronjob.service /usr/local/bin/task.sh

Check logs:

    journalctl -u mycronjob.service

This gives you structured logging with timestamps and metadata.

---

## Log Rotation

If you generate logs:

    /var/log/backup.log

Make sure they rotate:

Create `/etc/logrotate.d/backup`:

    /var/log/backup.log {
        weekly
        rotate 4
        compress
        missingok
        notifempty
    }

Cron output that grows forever will eventually fill your disk. Not fun.

---

## Capturing Only Errors

    0 2 * * * /usr/local/bin/task.sh 2>> /var/log/task.err

Useful when you want a clean log unless something breaks.

---

## Quick Test

Force cron to run a job and capture output to verify paths, perms, and output:

    run-parts --test /etc/cron.daily

---