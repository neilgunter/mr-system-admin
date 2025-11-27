# Building Local Repos

Sometimes your servers can’t (or shouldn’t) talk to the internet. That’s when you build a local mirror of RHEL repos. It keeps updates fast, predictable, and under your control.

---

## The Basics

On a server with access to Red Hat’s repos:

    dnf reposync --repoid=rhel-8-baseos-rpms --download-path=/var/repos/baseos --downloadcomps --download-metadata

This pulls down all packages and metadata for the chosen repo.

*Pro Tip*: Always use `--download-metadata`. Without it, clients can’t resolve dependencies.

---

## Serving the Repo

Expose it over HTTP:

    dnf install httpd -y
    systemctl enable --now httpd

Drop a symlink so Apache can serve it:

    ln -s /var/repos/baseos /var/www/html/baseos

Clients can now point to:

    http://yourserver/baseos

---

## Creating Repo Metadata

If you copied packages manually, regenerate metadata:

    createrepo_c /var/repos/custom

*Pro Tip*: Run this again anytime you add/remove RPMs.

---

## Configuring Clients

On each client, create a repo file:

    /etc/yum.repos.d/local-baseos.repo

Contents:

    [local-baseos]
    name=Local BaseOS Repo
    baseurl=http://yourserver/baseos
    enabled=1
    gpgcheck=0

---

## Syncing Regularly

Set up a cron or systemd timer to re-sync nightly or weekly, depending on how fresh you need updates.

---

## When to Use

- Air-gapped environments  
- Faster installs/updates on big fleets  
- Controlling exactly which packages/versions are available  

---