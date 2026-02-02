# Scoreboard Overlay for Streamlabs (iOS)

A simple, transparent scoreboard overlay you can use as a **custom URL layer** in the Streamlabs Mobile app on iOS.

## What’s included

- **`index.html`** – The overlay (Team 1 vs Team 2, scores, optional period). Designed for 16:9 (1920×1080), transparent background.
- **`control.html`** – Control page to set team names, scores, and period, then get the overlay URL to paste into Streamlabs.

## Using it with Streamlabs on iOS

1. **Host the overlay**  
   Put these files on a public URL (e.g. GitHub Pages, Netlify, or your own server). See [Hosting](#hosting) below.

2. **In Streamlabs Mobile (iOS):**
   - Open the app → **menu (☰)** → **Scenes** → create or edit a scene.
   - Tap **Layers** (top right) → **+** (Add layer).
   - Choose **Custom item** → **URL** (or “Link to a website”).
   - Paste the **overlay URL** (from the control page).  
     Example:  
     `https://your-site.com/scoreboard-overlay/index.html?team1=Red&team2=Blue&s1=5&s2=3`

3. **Position the layer**  
   Resize and move the URL layer in the editor. Stream output is 16:9; edit in landscape so it matches what viewers see.

4. **Updating scores**  
   Open **control.html** (on phone or computer), change team names/scores/period, then:
   - **Copy overlay URL** and paste it into the same Streamlabs layer (replace the old URL), or  
   - Use a new overlay URL each time you go live with different teams/scores.

## Overlay URL parameters

You can build the overlay URL yourself. All parameters are optional.

| Parameter | Description | Example |
|-----------|-------------|--------|
| `team1`  | Team 1 name | `team1=Red%20Squad` |
| `team2`  | Team 2 name | `team2=Blue%20Squad` |
| `s1`     | Team 1 score | `s1=10` |
| `s2`     | Team 2 score | `s2=8` |
| `period` | Period/round label (e.g. “Half 2”) | `period=Round%203` |

Example:  
`index.html?team1=Home&team2=Away&s1=21&s2=14&period=Final`

## Hosting

The overlay must be loaded from a **public URL** so Streamlabs Mobile can fetch it.

### Option A: GitHub Pages

1. Create a repo, push `index.html` and `control.html` into it.
2. Repo **Settings** → **Pages** → Source: **Deploy from branch** → branch `main` (or `master`), folder **/ (root)**.
3. Your overlay URL will be:  
   `https://<username>.github.io/<repo-name>/index.html`  
   Control page:  
   `https://<username>.github.io/<repo-name>/control.html`

### Option B: Netlify

1. Sign up at [netlify.com](https://www.netlify.com).
2. Drag the folder containing `index.html` and `control.html` into the Netlify deploy area, or connect a Git repo.
3. Use the provided URL, e.g.  
   `https://your-site.netlify.app/index.html`  
   and  
   `https://your-site.netlify.app/control.html`.

### Option C: Host from an always-on machine

You can serve the overlay from a PC or NAS that’s always on. Streamlabs on your phone must be able to reach it over the internet (see below).

**1. Run a simple web server on that machine**

- **Windows (Python):**  
  Open a command prompt in the `scoreboard-overlay` folder and run:
  ```bash
  python -m http.server 8080
  ```
  Or double‑click **`serve.bat`** (serves on port 8080).

- **Windows (PowerShell, no Python):**
  ```powershell
  cd path\to\scoreboard-overlay
  npx -y serve -p 8080
  ```
  (Requires Node.js.)

- **Mac/Linux:**
  ```bash
  cd scoreboard-overlay
  python3 -m http.server 8080
  ```

Then on the same machine, open:  
`http://localhost:8080/control.html` to get overlay URLs.

**2. Make it reachable from the internet**

Your phone (and Streamlabs) need a **public URL** that points to this server. Two options:

| Approach | Pros | Cons |
|----------|------|------|
| **Tunnel (recommended)** | No router config, works with dynamic IP, HTTPS included | Uses a third‑party service |
| **Port forwarding** | No third party | Router setup, dynamic IP / DNS, HTTPS is extra work |

**Option 2a: Tunnel (easiest)**  
Use a tunnel so the internet can reach your local server:

- **[ngrok](https://ngrok.com)** (free tier):  
  `ngrok http 8080`  
  You get a URL like `https://abc123.ngrok.io`. Overlay URL:  
  `https://abc123.ngrok.io/index.html?team1=...&team2=...&s1=0&s2=0`  
  Control page: `https://abc123.ngrok.io/control.html`  
  (Free URLs can change each time you start ngrok; paid gives a fixed subdomain.)

- **[Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)** (free):  
  Install `cloudflared`, then run a tunnel to `localhost:8080`. You get a fixed `*.trycloudflare.com` or your own domain with HTTPS.

In Streamlabs, use the **HTTPS** overlay URL the tunnel gives you.

**Option 2b: Port forwarding**  
On your router, forward port 80 (or 8080) to the always‑on machine’s local IP. Then:

- If your ISP gives you a **static IP**, overlay URL is:  
  `http://YOUR_PUBLIC_IP/index.html?...`  
  (Use port in the URL if you forwarded 8080: `http://YOUR_PUBLIC_IP:8080/index.html?...`.)

- If your IP **changes**, use a free **Dynamic DNS** service (e.g. No-IP, Duck DNS) and use that hostname instead of the IP.

**HTTPS:** Some embed contexts expect HTTPS. With port forwarding, you’d need a certificate and a reverse proxy (e.g. Caddy). Tunnels give HTTPS without that.

**3. Use the overlay in Streamlabs**  
In Streamlabs Mobile, add a **Custom item → URL** layer and paste the full overlay URL (e.g. your tunnel URL + `/index.html?team1=...&s1=0&s2=0`). Use the control page to build and copy that URL.

### Option D: Your own server (VPS / hosted)

Upload both files to a directory that’s served over HTTPS. Use the full URL to `index.html` (with query params if you want) as the overlay URL in Streamlabs.

## Tips

- **Bookmark the control page** on your phone so you can update scores and copy the new overlay URL quickly.
- Streamlabs uses **16:9** for the stream; the overlay is designed for 16:9. In portrait mode you’ll see letterboxing; position the overlay in landscape in the editor so it looks right on stream.
- For a “minimal” look, place the overlay in a corner and keep team names short.

## Files

```
scoreboard-overlay/
├── index.html   ← Overlay (use this URL in Streamlabs)
├── control.html ← Set scores and copy overlay URL
├── serve.bat    ← (Windows) Double‑click to run local server on port 8080
└── README.md
```

No build step or server required; plain HTML/CSS/JS.
