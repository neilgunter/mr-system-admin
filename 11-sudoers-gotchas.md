# Sudoers Gotchas

So you’ve got `sudo` working — great. Now let’s talk about why `/etc/sudoers` is one of the most misconfigured files you’ll run into.

---

## Never Edit Directly

Always, always use:

    visudo

This checks syntax before saving. One bad character in `/etc/sudoers` can lock *everyone* out of root, and that’s how you end up on a 2 AM bridge call.

---

## Include Files

Many distros (including RHEL) support `/etc/sudoers.d/` includes.  
That’s the clean way to manage custom rules:

    /etc/sudoers
    └── includes /etc/sudoers.d/*

*Pro Tip:* Drop per-team or per-app rules in `/etc/sudoers.d/` with descriptive filenames. Way easier to audit later.

---

## Common Screw-ups

- Granting `ALL=(ALL) NOPASSWD: ALL` to everyone.  
  → Congrats, you’ve basically disabled sudo.

- Forgetting to allow `tty`. Some commands break if `requiretty` is set and you’re using automation.

- Overlapping rules. The *last matching line wins*, which can override something you thought was locked down.

---

## Secure Patterns

- Prefer command-specific rules, e.g.:

    %deploy  ALL=(ALL) NOPASSWD: /bin/systemctl restart httpd

- Use groups (`%wheel`, `%devops`) instead of adding users one by one.

- Review with:

    sudo -l -U username

That shows exactly what a user can run.

---


