# 🏴‍☠️ Bandit — Level 14 → 15

| | |
|---|---|
| **Event / Platform** | OverTheWire: Bandit |
| **Category** | Network |
| **Difficulty** | ⭐⭐ |
| **Date solved** | 2026-07-26 |

## 🔎 Recon — Reading the Battlefield

Bandit is a beginner wargame played over SSH. The goal of each level is to find the password for the next. The Level 15 briefing says:

> The password for the next level can be retrieved by submitting the password of the current level to **port 30000 on localhost**.

So this isn't about files — it's about **talking to a listening TCP service**. First I grab the current level's password (stored on the box) and confirm something is listening:

```bash
cat /etc/bandit_pass/bandit14
nc -zv localhost 30000   # verify the port is open
```

## ⚔️ Exploitation — The Killing Move

Connect to the service with `netcat` and send the level-14 password. The server replies with the next password:

```bash
cat /etc/bandit_pass/bandit14 | nc localhost 30000
# -> Correct!
# -> <bandit15 password>
```

That returned string is the password for `bandit15`. Log in to confirm:

```bash
ssh bandit15@localhost -p 2220
```

**Why it works:** many services are just text protocols over TCP. `nc` (netcat) is the “TCP/IP swiss-army knife” — pipe data in, read the response out. This is the same primitive behind banner grabbing and manual protocol probing.

## 🩸 Flag

```
<bandit15 password — solve it yourself>
```

## 🧠 Lessons Refined

- **`nc host port`** is your manual client for any raw TCP service.
- Pipe input with `cat file | nc ...`; add `-zv` just to test if a port is open.
- The very next level (15→16) adds **TLS** — same idea, but swap `nc` for `openssl s_client -connect localhost:30001`.
- Learning to speak to raw sockets by hand demystifies how higher-level tools work.
