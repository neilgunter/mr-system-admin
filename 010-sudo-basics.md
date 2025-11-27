# Getting Around as Root (or Sudo)

The very first thing you’ll deal with in a new environment:  
“How do I run commands as root without breaking stuff or locking myself out?”  

Every company does this a little differently. Some places drop you into `root` on day one (yikes). Others make you jump through five sudo prompts and a YubiKey dance just to `cat` a log. Either way, here’s what you need.  

---

## The Basics

- Use `sudo` for almost everything.  
- Direct `root` logins are usually (and should be) disabled.  
- Test your access early, before you actually need it in production.  

    ```bash
    # Check if you even *have* sudo
    sudo -l

    # Run a harmless test command
    sudo whoami
    ```

If that says `root`, congrats — you’re in business.  

---

## Common Gotchas

- **Wrong group membership** → You might need to be in the `wheel` group (RHEL default).  
- **Password mismatch** → Sometimes sudo uses your *user* password, not root’s.  
- **Timeouts** → `sudo` caches your creds for ~5 minutes. After that, you’ll be asked again.  

*Pro Tip:* If `sudo` feels broken, double-check `/etc/sudoers` (but *never* edit it directly — use `visudo`).  

---

## Secure Habits

- **Don’t** use `sudo su -` as your daily driver — that’s just lazy root.  
- **Do** prefix commands with `sudo` so there’s a record in logs.  
- **Do** limit your sudoers rules to what’s needed (`/bin/systemctl`, not `ALL=(ALL)`).  

---

## When Sudo Doesn’t Work  

Sometimes the environment is misconfigured or you’re on a restricted account. In those cases:  

    ```bash
    # Switch to root with a known password (if enabled)
    su -

    # Or request temporary access via your team’s process (ticket, key, etc.)
    ```

If neither works, congratulations — you’ve found your first *helpdesk ticket* opportunity.  

---

