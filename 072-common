# Common Misunderstandings (Systemd)

Systemd is powerful, but it’s also opinionated. Most of the pain comes from assuming it behaves like old init systems. Here are the misconceptions that cause real trouble.

---

## “I edited the unit file, but nothing changed.”

Systemd caches unit files.  
If you don’t reload, your edits do nothing.

    systemctl daemon-reload

Then:

    systemctl restart app.service

*Pro Tip*: Make it a habit — edit → reload → restart.

---

## “The service says it’s running, but my process isn’t.”

Systemd doesn't care what *you* think running means.  
It cares what **Type=** the unit declares.

Common types:
- `simple` → systemd assumes the service is running after exec
- `forking` → service forks to the background
- `notify` → service signals readiness

If Type is wrong, systemd may mark a service active even though it died instantly.

Check it:

    systemctl cat app.service

---

## “My service keeps restarting forever.”

Likely caused by the Restart setting:

    Restart=always

or:

    Restart=on-failure

Combined with a broken ExecStart, you get a restart loop.

Check status:

    systemctl status app.service

and logs:

    journalctl -u app.service -f

---

## “Why won’t systemd pick up my environment variables?”

Because putting them in `/etc/environment` doesn’t always work.

Use overrides:

    systemctl edit app.service

Then add:

    [Service]
    Environment="APP_MODE=prod"

And reload daemon after.

---

## “I disabled the service but it still starts.”

Disabling stops autostart at boot.  
It does *not* stop units from being triggered by dependencies, sockets, or timers.

Example:

- A socket unit can auto-start a service  
- Another service can `Wants=` or `Requires=` it

Check:

    systemctl list-unit-files | grep app
    systemctl list-dependencies --reverse app.service

---

## “Stopping the service didn’t stop everything.”

Many services spawn child processes.  
If the unit isn’t configured to track them (`KillMode=process` vs `control-group`), leftovers stick around.

Check the KillMode in the unit:

    systemctl cat app.service

---

## “systemctl reload doesn’t reload config.”

A reload only works if the service supports it internally.

Check if the unit has:

    ExecReload=

If not, systemd can’t reload the service — you need to restart it.

---

## “Status says failed, but nothing’s happening.”

Look at the exit code:

    systemctl status app.service

Failed can mean:
- Wrong permissions  
- Missing file  
- Bad path  
- Misconfigured ExecStart  

Then go to the logs:

    journalctl -u app.service

---