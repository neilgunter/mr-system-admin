# Why Can’t I Ping the Gateway?

Classic problem: you bring up an interface, it has an IP, but the gateway is radio silent. Don’t panic — work through it systematically.

---

## Step 1: Check the Interface

    ip addr show
    ip route

- Do you actually have the right IP/subnet?  
- Is there a default route pointing at the gateway?

*Pro Tip*: If you see multiple default routes, that’s often the culprit.

---

## Step 2: ARP

If you can’t ping, try:

    arp -n

or

    ip neigh show

If the gateway shows as *incomplete*, your box isn’t getting ARP replies.

---

## Step 3: Layer 2 Problems

- Wrong VLAN on the switch port  
- NIC not plugged where you think it is  
- Bonding/teaming misconfigurations  

If you’re in a VM, double-check the vSwitch/port group config.

---

## Step 4: Firewalld / Iptables

Sometimes the firewall blocks outbound ICMP:

    firewall-cmd --list-all

*Pro Tip*: Don’t drop ICMP on servers — you’ll just make troubleshooting harder.

---

## Step 5: SELinux

Rare, but SELinux can interfere with DHCP or weird ICMP cases. Check audit logs if everything else looks fine.

---

## Step 6: The Old Cable Test

If nothing works, and it’s a physical box… check the cable. Yes, really.

---