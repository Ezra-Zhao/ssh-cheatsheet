# SSH Cheatsheet — Commands That Actually Work

One page. Twenty-eight entries: connecting, keys, `~/.ssh/config`, the forwarding trio, jump hosts, file transfer, and server hardening. Each entry tells you **what it does**, **a command you can copy-paste**, and **tags**. No made-up flags — every flag and config keyword checked against the OpenSSH 9.6 man pages (`ssh`, `ssh_config`, `ssh-keygen`, `scp`), and the config examples parsed for real with `ssh -G`.

> 🌍 Read this project in your language: [中文](README.md) · [Español](README.es.md) · [Português](README.pt.md)

Docs: https://www.openssh.com/

---

## 🔌 Connecting — the basics

### `ssh user@host` — connect, with a non-standard port
```bash
ssh deploy@example.com            # default port 22
ssh -p 2222 deploy@example.com    # -p picks the port
ssh -l deploy example.com         # -l = login name (the same as user@)
```
🏷️ `#connect` `#basics` `#must-know`
### `ssh -i` — log in with a specific key
```bash
ssh -i ~/.ssh/id_ed25519 deploy@example.com   # use exactly this private key
```
🏷️ `#connect` `#keys`
### `ssh -v` — debug a failed login
```bash
ssh -v deploy@example.com     # watch the handshake; -vv / -vvv for more detail
# the lines that matter: "Offering public key" → which key was tried,
# "Authentication succeeded" / "Permission denied" → where it ended
```
🏷️ `#debug` `#connect` `#troubleshooting`
### Keepalive — stop idle sessions from freezing
```bash
ssh -o ServerAliveInterval=60 -o ServerAliveCountMax=3 deploy@example.com
# ping the server every 60s; give up after 3 unanswered pings
# (both are also ~/.ssh/config keywords — see the config section)
```
🏷️ `#connect` `#keepalive`
### Escape sequences — kill a frozen session
```bash
# type these INSIDE a live (or hung) ssh session; ~ must be the first char after Enter
<Enter>~.       # disconnect NOW — the rescue for a session that stopped responding
<Enter>~^Z      # background ssh itself and get your local shell back
```
🏷️ `#connect` `#rescue` `#must-know`

---

## 🔑 Keys

### `ssh-keygen` — create an Ed25519 key pair
```bash
ssh-keygen -t ed25519 -C "you@example.com"
# private key: ~/.ssh/id_ed25519      → never share it, never commit it
# public key:  ~/.ssh/id_ed25519.pub  → this is the one you upload
# set a passphrase: it's the second factor protecting the file
```
🏷️ `#keys` `#setup` `#must-know`
### `ssh-copy-id` — install your public key on a server
```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub deploy@example.com
# appends the key to the server's ~/.ssh/authorized_keys;
# you log in with your password one last time — after that, the key does it
```
🏷️ `#keys` `#setup`
### `ssh-agent` + `ssh-add` — type the passphrase once per session
```bash
eval "$(ssh-agent -s)"        # start the agent and load its env vars
ssh-add ~/.ssh/id_ed25519     # unlock a key into the agent
ssh-add -l                    # list the fingerprints the agent holds
ssh-add -D                    # remove ALL keys from the agent
```
🏷️ `#keys` `#agent`
### `ssh-keygen -p` — change a key's passphrase
```bash
ssh-keygen -p -f ~/.ssh/id_ed25519   # asks for the old passphrase, then the new one twice
```
🏷️ `#keys` `#security`
### Key file hygiene — permissions and fingerprints
```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519            # ssh REFUSES to use a private key others can read
chmod 644 ~/.ssh/id_ed25519.pub
ssh-keygen -l -f ~/.ssh/id_ed25519.pub # print the fingerprint — compare before you trust
```
🏷️ `#keys` `#security` `#permissions`

---

## 📝 ~/.ssh/config — stop memorizing addresses

### Host alias — the template worth copying
```sshconfig
Host web
    HostName example.com
    User deploy
    Port 2222
    IdentityFile ~/.ssh/id_ed25519
```
```bash
ssh web    # one word now replaces user + host + port + key
```
🏷️ `#config` `#aliases` `#must-know`
### `Host *` — global defaults (put them LAST)
```sshconfig
Host *
    ServerAliveInterval 60
    ServerAliveCountMax 3
```
ssh_config uses the **first** value it finds for each keyword — the opposite of most config files. So specific `Host` blocks go on top, and `Host *` defaults belong at the bottom of the file.
🏷️ `#config` `#defaults`
### `IdentitiesOnly` — fix "Too many authentication failures"
```sshconfig
Host web
    HostName example.com
    User deploy
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes
```
If your agent holds many keys, a server can reject you before ssh ever tries the right one. `IdentitiesOnly yes` makes ssh offer only the key named in the config.
🏷️ `#config` `#keys` `#troubleshooting`
### Wildcard hosts — one block per fleet
```sshconfig
Host prod-*
    User ops
    IdentityFile ~/.ssh/id_ed25519_prod

Host prod-db-01 prod-web-01
    Port 2222
```
`ssh prod-db-01` picks up both blocks: user + key from the wildcard, port from the list.
🏷️ `#config` `#wildcards`

---

## 🔀 Port forwarding — the trio

### `-L` — local forward: bring a remote port to you
```bash
ssh -L 8080:localhost:80 deploy@example.com
# http://localhost:8080 on YOUR machine now hits port 80 on the server
ssh -L 5433:db.internal:5432 deploy@example.com
# ...or reach a third machine (db.internal) through the server you're logged into
```
🏷️ `#tunnels` `#local-forward` `#must-know`
### `-R` — remote forward: expose your local service to the server side
```bash
ssh -R 9000:localhost:3000 deploy@example.com
# on the SERVER, localhost:9000 now reaches the app on YOUR localhost:3000
# default bind is the server's loopback only — other machines can't reach it;
# exposing it further needs GatewayPorts yes in the SERVER's sshd_config (an admin call, not a client flag)
# classic use: let a colleague test the dev server running on your laptop
```
🏷️ `#tunnels` `#remote-forward`
### `-D` — dynamic forward: a SOCKS proxy in one flag
```bash
ssh -D 1080 deploy@example.com
# point your browser or app at SOCKS5 proxy localhost:1080 —
# its traffic exits through the server
```
🏷️ `#tunnels` `#socks` `#proxy`
### `-fN` — tunnel-only, in the background
```bash
ssh -fN -o ExitOnForwardFailure=yes -L 8080:localhost:80 deploy@example.com
# -f: background after auth   -N: no remote command, tunnel only
# ExitOnForwardFailure: exit with an error if the local port can't be bound,
# instead of "working" silently with no tunnel
```
🏷️ `#tunnels` `#background`

---

## 🪜 Jump hosts — ProxyJump

### `ssh -J` — hop through a bastion
```bash
ssh -J user@bastion.example.com deploy@192.0.2.10              # one hop
ssh -J jump1.example.com,jump2.example.com deploy@192.0.2.10    # chained hops, comma-separated
# 192.0.2.x is the documentation range — swap in your real internal address
```
🏷️ `#jump-host` `#bastion` `#must-know`
### `ProxyJump` in config — bake the hop into the alias
```sshconfig
Host bastion
    HostName bastion.example.com
    User ec2-user
    IdentityFile ~/.ssh/id_ed25519

Host internal
    HostName 192.0.2.10
    User deploy
    ProxyJump bastion
```
```bash
ssh internal    # the hop happens automatically —
                # scp / sftp / rsync to "internal" get it for free too
```
🏷️ `#jump-host` `#config`

---

## 📤 File transfer

### `scp` — copy files both ways
```bash
scp ./app.tar.gz deploy@example.com:/tmp/         # upload
scp deploy@example.com:/var/log/app.log ./         # download
```
🏷️ `#files` `#scp`
### `scp -P` / `-r` — custom port and whole directories
```bash
scp -P 2222 -r ./site/ deploy@example.com:/var/www/
# ⚠️ scp's port flag is CAPITAL -P; ssh uses lowercase -p.
# This mix-up is the #1 reason "scp works but ssh doesn't" (and vice versa).
```
🏷️ `#files` `#scp`
### `rsync -e ssh` — incremental, re-runnable sync
```bash
rsync -avz -e ssh ./site/ deploy@example.com:/var/www/site/
rsync -avz -e "ssh -p 2222" deploy@example.com:/var/log/ ./logs/
# only the differences cross the wire, so re-running after a network
# drop is cheap — the default tool for syncing whole trees over ssh
```
🏷️ `#files` `#rsync` `#sync`
### `sftp` — an interactive file shell
```bash
sftp deploy@example.com
sftp> put ./report.csv /tmp/    # upload
sftp> get /var/log/app.log      # download
sftp> ls /var/log               # remote listing (lls = local listing)
sftp> exit
```
🏷️ `#files` `#sftp`

---

## 🛡️ Server hardening — /etc/ssh/sshd_config

Server side. Change **one thing at a time**, keep your current session open, and prove you can still log in (in a second terminal) before you close it.

### `PermitRootLogin` — no direct root login
```text
PermitRootLogin no
```
Log in as a normal user and use `sudo`. Middle ground: `PermitRootLogin prohibit-password` — root may still log in with a key, never with a password.
🏷️ `#sshd` `#hardening` `#must-know`
### Keys only — turn off password authentication
```text
PubkeyAuthentication yes
PasswordAuthentication no
KbdInteractiveAuthentication no
```
⚠️ Verify key-based login works in a **new** session first. With passwords off, a lost key means you're locked out and only console access gets you back in. The third line matters too: on PAM-based systems the password prompt can survive through keyboard-interactive even with PasswordAuthentication no.
🏷️ `#sshd` `#hardening` `#keys`
### `AllowUsers` + `MaxAuthTries` — shrink the attack surface
```text
AllowUsers deploy ops
MaxAuthTries 3
```
`AllowUsers`: only these accounts may log in via SSH at all. `MaxAuthTries`: the server drops a connection after this many failed attempts (default is 6).
🏷️ `#sshd` `#hardening`
### Change the port + the safe edit drill
```text
Port 2222
```
```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak   # 1. backup
sudo sshd -t                                            # 2. syntax check — silence means OK
# (and open the new port in your firewall before step 3, if you changed it)
sudo systemctl reload ssh                               # 3a. Debian/Ubuntu: service name is "ssh"
sudo systemctl reload sshd                              # 3b. RHEL family: service name is "sshd"
# reload keeps existing sessions alive — verify a fresh login works before logging out
```
🏷️ `#sshd` `#hardening` `#ops`

---

## Contributing

Found a wrong command, an outdated flag, or a bad translation? Pull requests are welcome — keep entries to the format above (one-liner / bash block / tags) and **only real, verified commands; when in doubt, leave it out**.

## License

MIT © 2026 Guangyi Zhao. See [LICENSE](LICENSE).
