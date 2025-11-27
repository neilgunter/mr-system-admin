# SELinux: Quick Diagnosis

Love it or hate it, SELinux is here to stay on RHEL. Disabling it may “fix” things, but that’s like cutting the seatbelt out of your car because it chafes. Better to learn how to check what it’s actually doing.

---

## Step 1: Check the Mode

    getenforce

- `Enforcing` → SELinux is active.  
- `Permissive` → Logs denials but doesn’t enforce.  
- `Disabled` → Don’t be that person.

---

## Step 2: Look at the Logs

Audit logs are your first stop:

    ausearch -m avc -ts recent
    tail -f /var/log/audit/audit.log

Or, use the friendlier tool:

    sealert -a /var/log/audit/audit.log

*Pro Tip*: If you don’t see AVCs, it’s not SELinux causing the issue.

---

## Step 3: Identify the Context

Every file and process has a security context:

    ls -Z /var/www/html/index.html
    ps -eZ | grep httpd

If the labels don’t match what SELinux expects, you’ll get denials.

---

## Step 4: Quick Fixes

- Restore default labels:

      restorecon -Rv /var/www/html

- Allow common services:

      setsebool -P httpd_can_network_connect 1

---

## Step 5: When in Doubt

If you’re not sure, set SELinux to permissive temporarily:

    setenforce 0

Re-run your test. If it works now, SELinux is indeed blocking it.  
But don’t leave it off — fix the policy instead.

---