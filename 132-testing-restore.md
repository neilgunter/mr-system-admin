# Testing Restores

Backups are comforting lies until you’ve actually tested a restore.  
A backup that’s never been restored is just a folder full of hope — and hope is not a recovery strategy.

This chapter is how you verify that your backups work **before** the outage, not during it.

---

## Rule #1: Test on a Different System

Never test restores on the production box.

Use:
- A VM  
- A throwaway cloud instance  
- A lab server  
- A chroot environment  

If your restore breaks something, you want it breaking somewhere safe.

---

## Basic File Restore Test

Pull data from the backup target:

    rsync -avh backup:/data/ /restore-test/

Check:
- Permissions  
- Owners  
- Symlinks  
- Timestamps  
- SELinux labels (if applicable)

Fix SELinux labels:

    restorecon -Rv /restore-test

*Pro Tip*: If the restored data “looks right” but apps still fail, it's almost always permissions or SELinux.

---

## Database Restore Tests

Never trust filesystem-level database backups without validation.

MySQL / MariaDB:

    mysqldump -u root -p dbname > dump.sql
    mysql -u root -p -e "CREATE DATABASE testdb;"
    mysql -u root -p testdb < dump.sql

PostgreSQL:

    pg_dump dbname > dump.sql
    createdb testdb
    psql testdb < dump.sql

If the dump imports cleanly, the backup is valid.

---

## VM / Image Restore Tests

If backups include VM images or snapshots:

- Restore to a **new VM**  
- Boot it  
- Confirm services start  
- Confirm logs and configs are intact  
- Check network identity (avoid IP/MAC collisions)

This is the only real test of a bare-metal or hypervisor-level backup.

---

## Application-Level Restore Tests

Apps with complex data paths (GitLab, Jira, Confluence, etc.) often require their own restore procedures.

Test:
1. Restore config files  
2. Restore database  
3. Restore application data  
4. Launch app and verify functional behavior  

If the app loads but the UI is broken, the restore is incomplete.

---

## Disaster Scenarios to Simulate

Test at least one of these each quarter:

- “The disk died; rebuild the server.”  
- “Someone deleted the database.”  
- “We deployed a broken config at 2 AM.”  
- “Ransomware encrypted everything but backups.”  

Even simple simulations find problems you wouldn't catch otherwise.

---

## Validate Backup Integrity

For rsync-based backups:

    rsync --dry-run --checksum -avh /source/ /backup-target/

This confirms files match bit-for-bit.

For tar archives:

    tar -tvf archive.tar.gz

For LVM snapshots:

    lvs

Check for:
- Corruption  
- Unexpected growth  
- Invalidated snapshots  

For object storage:

    aws s3 ls bucket/hostname/
    aws s3 sync --dryrun

---

## Restore Timing

Measure how long the restore takes.

In a crisis, “full restore in 18 hours” may be unacceptable.  
If you can’t restore within the business’s tolerance, you need better backups (or partial restores).

---

## Document the Restore Procedure

Write down **exact steps**:
- Commands  
- Locations  
- Server dependencies  
- Required credentials  

Store the doc where others can find it.

A backup no one knows how to restore is as useless as having none.

---

## The Real Goal

Backups aren’t for peace of mind.  
Backups exist so your restore works:

- under pressure  
- when people are panicking  
- when you’re half-awake at 3 AM  
- when production is ablaze  

Testing restores is how you make sure that story ends well.

---
