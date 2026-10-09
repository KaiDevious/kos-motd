# Broadcast MOTD + Notify

A tiny, open-source service that lets **any Linux distro or setup** broadcast two things from a single GitHub repo:

- 📜 a **message of the day** shown in the terminal, and
- 🔔 **desktop pop-up notifications** (updates, announcements, whatever).

Edit a text file in your repo, commit, and every machine running the service sees it within a minute. No server, no backend — just GitHub and a couple of shell scripts.

> Originally built for **kOS** (a custom Kali-based distro), but it's distro-agnostic — use it for your own OS, homelab, classroom, team, anything.

## How it works
Each machine quietly fetches the raw text of two files on a timer:
- **`message.txt`** → shown in the terminal when it opens.
- **`notify.txt`** → shown as a desktop pop-up when it changes.

To broadcast, you just edit those files and commit. That's it.

## Make it yours (2 steps)
1. **Fork this repo** (or copy `message.txt` and `notify.txt` into your own). This matters — if you point the scripts at *my* repo, *I* control your messages. Use your own so **you** do.
2. In the commands below, replace:
   - `YOURUSER/YOURREPO` → your repo
   - `MyOS` → whatever you want the notification title to say

## Setup

### 1. Message of the day (terminal)
**bash:**
```bash
echo 'curl -fsS --max-time 2 "https://raw.githubusercontent.com/YOURUSER/YOURREPO/refs/heads/main/message.txt?t=$(date +%s)" 2>/dev/null; echo' >> ~/.bashrc
```
**zsh:**
```bash
echo 'curl -fsS --max-time 2 "https://raw.githubusercontent.com/YOURUSER/YOURREPO/refs/heads/main/message.txt?t=$(date +%s)" 2>/dev/null; echo' >> ~/.zshrc
```

### 2. Desktop notifications (checks every minute)
```bash
mkdir -p ~/.local/bin ~/.config/autostart ~/.cache

cat > ~/.local/bin/broadcast-notify.sh << 'EOF'
#!/bin/bash
NAME="MyOS"   # <-- change to your OS / project name
URL="https://raw.githubusercontent.com/YOURUSER/YOURREPO/refs/heads/main/notify.txt"
STATE="$HOME/.cache/broadcast-last-notify"
sleep 15
while true; do
  MSG=$(curl -fsS --max-time 5 "$URL?t=$(date +%s)" 2>/dev/null)
  if [ -n "$MSG" ] && [ "$MSG" != "$(cat "$STATE" 2>/dev/null)" ]; then
    notify-send "$NAME" "$MSG"
    echo "$MSG" > "$STATE"
  fi
  sleep 60
done
EOF
chmod +x ~/.local/bin/broadcast-notify.sh

cat > ~/.config/autostart/broadcast-notify.desktop << EOF
[Desktop Entry]
Type=Application
Exec=$HOME/.local/bin/broadcast-notify.sh
Name=Broadcast Notify
EOF
```
Then **log out and back in** — it runs automatically from then on.

## Sending a broadcast
Edit the file on GitHub (or `git push` a change) and commit:
- change **`message.txt`** → updates everyone's terminal greeting
- change **`notify.txt`** → pops up a notification on everyone's desktop

## Tips
- Keep messages to **one line** so they look clean.
- Avoid quotes (`"` / `'`) in messages — they can break the display.
- Changes take up to ~1 minute to reach everyone (GitHub caches raw files briefly). The `?t=...` on the URL busts that cache.

## Requirements
- `curl` (preinstalled on most systems)
- `notify-send` for pop-ups (`libnotify-bin` on Debian/Ubuntu/Kali) — only needed for notifications, not the MOTD.

## License
MIT — free to use, fork, and modify. Originally made by **Kai** for kOS. 💙

YES THIS WAS WRITTEN WITH AI IM LAZY AF
