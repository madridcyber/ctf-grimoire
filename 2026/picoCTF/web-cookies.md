# 🏴‍☠️ Cookies

| | |
|---|---|
| **Event / Platform** | picoCTF (picoGym practice) |
| **Category** | Web |
| **Difficulty** | ⭐ |
| **Date solved** | 2026-07-26 |

## 🔎 Recon — Reading the Battlefield

The challenge serves a small web app with a search box that asks *“Who doesn't love cookies?”* Typing `snickerdoodle` reloads the page with a message and, crucially, sets a cookie:

```
Cookie: name=-1
```

Opening DevTools → **Application → Cookies** (or `document.cookie` in the console) shows a single integer-valued cookie named `name`. The value `-1` for the default search is a loud hint: the app is indexing a list of cookie *names* by number, and some index unlocks the flag.

## ⚔️ Exploitation — The Killing Move

No injection needed — just tamper with the cookie value and walk the index space. I automated the walk with `curl` so I didn't have to click 30 times:

```bash
for i in $(seq 0 30); do
  echo "== name=$i =="
  curl -s --cookie "name=$i" "$URL/check" | grep -Eo 'picoCTF\{[^}]*\}|<h1>[^<]*</h1>'
done
```

Most indices return a different cookie flavour (“snickerdoodle”, “chocolate chip”, …). One index flips the page into the success state and prints the flag. Setting that value manually in DevTools and refreshing shows it in the browser too.

**Why it works:** the server trusts a client-side value (`name`) as an authorization/index token. Cookies are attacker-controlled — never trust them for access decisions without a signature (e.g., signed/HMAC cookies) or server-side session state.

## 🩸 Flag

```
picoCTF{redacted-solve-it-yourself}
```

## 🧠 Lessons Refined

- **Client-side data is not a secret.** Cookies, hidden form fields, and localStorage are all editable by the user.
- When you see a numeric token, **enumerate it** — small integer keyspaces fall in seconds.
- Scripting a boring click-through with `curl` + `grep` is almost always faster than doing it by hand.
- Fix: sign cookies (HMAC) or keep authorization state on the server keyed by an unguessable session ID.
