# nmcli vs nmtui

On RHEL, NetworkManager is king. You’ll manage interfaces with either `nmcli` (the CLI tool) or `nmtui` (the text UI). Both talk to the same backend — pick whichever keeps you sane.

---

## nmcli (Command Line)

Fast, scriptable, powerful. But it’s also verbose.

Examples:

    nmcli device status
    nmcli con show
    nmcli con up ens192
    nmcli con mod ens192 ipv4.addresses 192.168.1.50/24

*Pro Tip*: Always run `nmcli device status` first. It shows which interfaces NetworkManager even sees.

---

## nmtui (Text UI)

Runs in curses, friendlier for quick edits:

    nmtui

From there you can:

- Edit connections  
- Set hostnames  
- Bring interfaces up/down  

*Pro Tip*: `nmtui` is great on a new system before you memorize `nmcli`’s syntax.

---

## When to Use Which

- **nmcli** → automation, scripting, consistent across servers.  
- **nmtui** → fast fixes when you’re SSH’d in or stuck on a console.  

---

## Troubleshooting

If neither works, NetworkManager may not be running:

    systemctl status NetworkManager
    systemctl enable --now NetworkManager

---