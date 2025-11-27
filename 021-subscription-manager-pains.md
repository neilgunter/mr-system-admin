# Subscription-Manager Pains

If you’re running RHEL, you’ll quickly meet `subscription-manager`. It’s Red Hat’s way of saying “pay us first, then you get updates.” On a fresh system, this is usually the first blocker you hit.

---

## Registering

    subscription-manager register --username yourRHNuser --password yourRHNpass

Or if you’re using an activation key:

    subscription-manager register --activationkey=mykey --org=myorg

*Pro Tip*: Never paste personal RHN creds into automation. Use activation keys instead.

---

## Attaching Subscriptions

After registering:

    subscription-manager attach --auto

This grabs a suitable subscription if your org has available entitlements.

Check status:

    subscription-manager status

---

## Common Problems

- **System not registered** → You’ll see repo errors like *“This system is not registered to Red Hat Subscription Management.”*  
- **No available entitlements** → Someone in procurement forgot to buy enough subs. Time for a polite ticket.  
- **Stale certs** → Sometimes certs expire or get corrupted. Fix with:

      subscription-manager refresh

---

## Working Offline

If your servers can’t reach Red Hat directly, you may need a Satellite server or local mirror. That way, `subscription-manager` points to internal repos instead of the internet.

*Pro Tip*: Always confirm which repos are enabled:

    subscription-manager repos --list-enabled

---

## Unregistering

When decommissioning or cloning a system:

    subscription-manager unregister

This prevents “ghost” systems from hogging entitlements.

---