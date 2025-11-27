# Crontab: Environment Issues

Cron doesn’t run commands the way your shell does. It strips out almost everything — environment variables, PATH, aliases — and then people wonder why their scripts fail at 2 AM but work perfectly when run manually.

---

## The Core Problem

Your interactive shell has:
- A full PATH  
- Loaded profiles (`~/.bashrc`, `/etc/profile`)  
- Aliases  
- Exports  
- Custom functions  

Cron has:
- A tiny PATH  
- No shell profiles  
- No aliases  
- No interactivity  

So commands that work fine in your terminal break instantly under cron.

---

## Always Set PATH

At the top of your crontab:

    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

This alone fixes half of all cron failures.

*Pro Tip*: Never rely on the default PATH. It varies across distros and environments.

---

## Use Full Paths

Instead of:

    backup.sh

Use:

    /usr/local/bin/backup.sh

Instead of:

    systemctl restart app.service

Use:

    /bin/systemctl restart app.service

Cron doesn’t guess. It won’t look for commands.

---

## Export Needed Variables

If your script relies on environment variables:

    MYTOKEN=abcd1234
    export MYTOKEN

Or set them inside the crontab before the job:

    MYTOKEN=abcd1234
    0 2 * * * /usr/local/bin/task.sh

---

## Scripts Need Shebangs

Your script should start with:

    #!/bin/bash

Without a proper shebang, cron might run it under `/bin/sh` instead — and that’s where subtle bugs crawl in.

---

## No Terminal, No Output

Cron has no TTY. Anything expecting interactivity will fail:
- sudo prompts  
- password prompts  
- commands needing curses  
- scripts using `read`  

If cron needs sudo, configure passwordless *for that specific command* in sudoers.

---

## Debugging

Force cron to show its true colors by simulating its environment:

    env -i /bin/bash -c '/usr/local/bin/script.sh'

That runs with an empty environment, just like cron.

---

## Check the Logs

System cron logs:

    journalctl -u crond

User cron logs:

    grep CRON /var/log/cron

You’ll often see `command not found` or `No such file or directory` — classic PATH issues.

---