Hey! If your looking at this JUST know-

This is NOT kOS. This is the custom service to send update/creator notifs and motds (message of the day) for Konsole.

Just note that the base is Kali Linux.

kOS hasnt been posted on this day (oct 8 2026) so just wait
Also kOS got deleted.. (whoops) So im revamping this.

How to use? 
For bash users:
echo 'curl -fsS --max-time 2 "https://raw.githubusercontent.com/yourprofile/your-motd-notif-sender/refs/heads/main/message.txt?t=$(date +%s)" 2>/dev/null; echo' >> ~/.bashrc
For zsh users:
echo 'curl -fsS --max-time 2 "https://raw.githubusercontent.com/yourprofile/your-motd-notif-sender/refs/heads/main/message.txt?t=$(date +%s)" 2>/dev/null; echo' >> ~/.zshrc
Update/Creator notifs?
mkdir -p ~/.local/bin ~/.config/autostart ~/.cache
cat > ~/.local/bin/kos-notify.sh << 'EOF'
#!/bin/bash
URL="https://raw.githubusercontent.com/yourprofile/your-motd-notif-sender/refs/heads/main/notify.txt"
STATE="$HOME/.cache/youros-last-notify"
sleep 15
while true; do
  MSG=$(curl -fsS --max-time 5 "$URL?t=$(date +%s)" 2>/dev/null)
  if [ -n "$MSG" ] && [ "$MSG" != "$(cat "$STATE" 2>/dev/null)" ]; then
    notify-send "kOS" "$MSG"
    echo "$MSG" > "$STATE"
  fi
  sleep 60
done
EOF
chmod +x ~/.local/bin/kos-notify.sh
cat > ~/.config/autostart/kos-notify.desktop << 'EOF'
[Desktop Entry]
Type=Application
Exec=/home/REPLACE_ME/.local/bin/youros-notify.sh
Name=[your os here] Notify
EOF
