# rev-tools

**Are you a slow typer (like me)?**  
**Tired of remembering all the payloads?**  
Same. These tiny helpers speed up real-world pentests without bloat.

## What’s inside
- **`revshells`** — prints copy‑paste reverse shells (Bash/PHP/Python/Perl/Netcat), Kali PHP webshell paths, and a UTF‑16LE Base64 PowerShell `-EncodedCommand` built from your chosen IP/port.
- **`server`** — the laziest HTTP file server wrapper you’ll ever use:
  - `server` → serves port **80** (uses sudo/authbind if needed)
  - `server 7777` → serves on **7777**
  - `server ./dir` → serves **./dir** on **80**
  - also supports `server 7777 ./dir` and `server ./dir 7777`

---

## Install (one-liners)
Install straight from your GitHub profile (**PahutieEutiphron**):

```bash
# revshells
sudo curl -fsSL https://raw.githubusercontent.com/PahutieEutiphron/rev-tools/main/revshells -o /usr/local/bin/revshells \
  && sudo chmod +x /usr/local/bin/revshells

# server
sudo curl -fsSL https://raw.githubusercontent.com/PahutieEutiphron/rev-tools/main/server -o /usr/local/bin/server \
  && sudo chmod +x /usr/local/bin/server
```

Or clone once and copy:
```bash
git clone https://github.com/PahutieEutiphron/rev-tools.git
cd rev-tools
sudo install -m 755 revshells /usr/local/bin/revshells
sudo install -m 755 server    /usr/local/bin/server
```

---

## Usage
### `revshells`
```bash
revshells                    # auto-picks tun0 or default route IP, port 443
revshells -i 10.10.14.7 -p 9001
```
Outputs:
- Bash/PHP/Python/Perl/Netcat reverse shells pre-filled with your IP/port
- Kali PHP webshell paths: `/usr/share/webshells/php/*`
- Ready-to-run **PowerShell -EncodedCommand** (UTF-16LE Base64) and the raw payload
- Quick listener + TTY upgrade tips

### `server`
```bash
server                       # serve current dir on :80 (sudo/authbind if needed)
server ./Tools               # :80 from ./Tools
server 7777                  # :7777 current dir
server 7777 ./Tools          # :7777 from ./Tools
server ./Tools 7777          # :7777 from ./Tools
```
Binds to `0.0.0.0` and prints a friendly URL using your host IP.

---

## Quick start (copy/paste)
```bash
# 1) Listener on Kali
rlwrap -r nc -lvnp 9001

# 2) Generate shells using auto IP
revshells -p 9001
# copy your preferred payload to the target

# 3) If you need to serve a file quickly
server 8000 ./Tools
# browse to http://<your-ip>:8000
```

---

## Tips & Notes
- `revshells` picks `tun0` first; if missing, it uses your default route IP.
- Prefer `rlwrap` listeners for command history and arrow keys.
- After catching a TTY, upgrade quickly (the tool prints the sequence):
  - `python3 -c 'import pty; pty.spawn("/bin/bash")'`
  - `export TERM=xterm-256color` → **Ctrl+Z** → `stty raw -echo; fg` → `reset`
  - `stty rows <N> columns <M>` if needed

### Serving on port 80 without password prompts
- **Use a high port**: `server 8000`
- **authbind** (per-port, safer):
  ```bash
  sudo apt-get install -y authbind
  sudo touch /etc/authbind/byport/80
  sudo chown "$USER":"$USER" /etc/authbind/byport/80
  sudo chmod 500 /etc/authbind/byport/80
  server                 # now :80 without sudo prompt
  ```
- **setcap** (broader; any python3 can bind <1024>):
  ```bash
  sudo setcap 'cap_net_bind_service=+ep' "$(readlink -f "$(which python3)")"
  server
  ```

---

## Update
```bash
sudo curl -fsSL https://raw.githubusercontent.com/PahutieEutiphron/rev-tools/main/revshells -o /usr/local/bin/revshells && sudo chmod +x /usr/local/bin/revshells
sudo curl -fsSL https://raw.githubusercontent.com/PahutieEutiphron/rev-tools/main/server    -o /usr/local/bin/server    && sudo chmod +x /usr/local/bin/server
```

## Uninstall
```bash
sudo rm -f /usr/local/bin/revshells /usr/local/bin/server
```

## Contributing
PRs and tiny improvements welcome (extra payloads, `socat` variants, Windows download cradles, etc.).

---

**Built by**: [PahutieEutiphron](https://github.com/PahutieEutiphron) — because memory is for vulns, not for payload syntax.
