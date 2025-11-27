# Watching Logs Live

Real-time logs are where you catch problems *as* they happen — not five minutes later when someone asks why production is on fire. `journalctl` does live-tail extremely well once you know how to drive it.

---

## The Classic Tail

    journalctl -f

This follows the full system log as new entries appear.  
Useful, but noisy.

---

## Follow a Specific Service

    journalctl -u sshd -f
    journalctl -u httpd -f
    journalctl -u app.service -f

This is the bread and butter. If you’re debugging *anything* systemd-managed, start here.

*Pro Tip*: Always watch the service you’re about to restart. That’s how you catch startup failures in real time.

---

## Combine Filters

Follow a service, errors only:

    journalctl -u app.service -p err.. -f

Or follow a specific priority level across the whole system:

    journalctl -p warning -f

---

## Follow Logs for a Specific Process

    journalctl _PID=1234 -f

Extremely useful when a single misbehaving process keeps throwing the same cryptic messages.

---

## Colorized Output

    journalctl -f --output=cat

Strips the metadata and shows only the message body — much easier to read during rapid-fire debugging.

---

## Timestamps You Can Actually Read

    journalctl -f --output=short-unix
    journalctl -f --output=short-iso

This helps when logs are coming in too fast to follow.

---

## When Debugging Over SSH

If your session is at risk of dropping:

    journalctl -u important.service -f -n 50

The `-n 50` gives you recent context so you don’t miss what happened before the disconnect.

---