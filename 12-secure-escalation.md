# Secure Escalation

You don’t want everyone running around as `root`. But you also don’t want 200 “ticket to restart a service” requests in your queue. The sweet spot is **least privilege escalation** — enough access to do the job, without handing over the keys to the kingdom.

---

## The Principle

- Give access only to the commands people need.  
- Log everything they run with `sudo`.  
- Keep it auditable and reversible.

---

## Examples

### Restarting a Service

Instead of full root:

    %appteam ALL=(ALL) NOPASSWD: /bin/systemctl restart httpd

Now your web folks can restart Apache without touching firewalld, LVM, or shadow passwords.

---

### Editing a Config File

Safer than giving shell access:

    %dbadmins ALL=(ALL) NOPASSWD: /usr/bin/vim /etc/my.cnf

*Pro Tip:* Sometimes it’s better to hand out `sudo cp` or `sudo tee` for specific files, to avoid the power of a full editor.

---

### Running Scripts

Wrap your logic in a controlled script:

    %deploy ALL=(ALL) NOPASSWD: /usr/local/bin/deploy.sh

Then lock down that script’s permissions so only admins can change it.

---

## What *Not* To Do

- `ALL=(ALL) NOPASSWD: ALL` → That’s root, no questions asked.  
- Giving `sudo su -` access → You lose command logging, and you’ll never know what actually happened.  
- Letting one person’s emergency hack become policy → Temporary rules have a way of living forever.

---

## Auditing Access

Check who can do what:

    sudo -l -U username

And periodically review `/etc/sudoers.d/` for zombie entries.

---