# When to Use systemd Timers Instead

Cron works, but it’s fragile, silent, and inconsistent across environments. Systemd timers, on the other hand, bring logging, scheduling sanity, dependency management, and clean integration with services.  
If you’re already using systemd (and you are), timers are often the better choice.

---

## Why Timers Beat Cron

- Logs go straight into `journalctl`  
- Clear success/failure status  
- Easy to start/stop on demand  
- Can depend on other units  
- Understand system boot ordering  
- Persistent timers run jobs missed during downtime

Cron can’t do half of that.

---

## Anatomy of a Timer Pair

You need **two files**:

### The service  
`/etc/systemd/system/backup.service`:

    [Unit]
    Description=Nightly backup job

    [Service]
    Type=oneshot
    ExecStart=/usr/local/bin/backup.sh

### The timer  
`/etc/systemd/system/backup.timer`:

    [Unit]
    Description=Run backup daily

    [Timer]
    OnCalendar=daily
    Persistent=true

    [Install]
    WantedBy=timers.target

*Pro Tip*: `Persistent=true` means if the system was down at the scheduled time, the job runs as soon as it boots.

---

## Common OnCalendar Formats

- Daily:

      OnCalendar=daily

- Every 5 minutes:

      OnCalendar=*:0/5

- Mondays at 3 AM:

      OnCalendar=Mon 03:00

- First day of every month:

      OnCalendar=monthly

---

## Enabling the Timer

    systemctl daemon-reload
    systemctl enable --now backup.timer

Check status:

    systemctl list-timers

Look for your timer and the next scheduled run.

---

## Running Jobs Manually

    systemctl start backup.service

This lets you test your job in a controlled way without waiting for the next timer tick.

---

## Logging

View job logs:

    journalctl -u backup.service

You get timestamps, exit codes, and stdout/stderr — everything cron should have provided but never did.

---

## When Cron Still Makes Sense

- Quick, temporary tasks  
- Legacy systems without systemd  
- User-level jobs that don’t need logging or reliability  

But on RHEL and related systems?  
Use timers for anything important.

---