
```bash
sudo apt install xrdp -y

sudo systemctl enable --now xrdp

sudo ufw allow 3389/tcp

sudo apt install xfce4 xfce4-goodies -y

echo xfce4-session > ~/.xsession

sudo systemctl restart xrdp
```

**可以通过windows的远程桌面连接**
