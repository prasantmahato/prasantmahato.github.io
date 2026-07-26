# Ridelink — offline phone-to-phone bike intercom

Three files: `index.html`, `sw.js`, `manifest.json`. No build step, no server code, no dependencies.

## Why it needs a one-time internet step

iOS Safari only allows microphone access (`getUserMedia`) on pages loaded over **HTTPS**.
There's no way around that from a plain web page. So this needs to be hosted somewhere
with HTTPS *once* so both phones can load it — after that, the service worker caches it
and the app opens and works with zero internet, using only your hotspot's local WiFi.

## Step 1 — Host it (5 minutes, free, do this before your ride)

Easiest option — **GitHub Pages**:
1. Create a free GitHub account if you don't have one.
2. Create a new public repo, e.g. `ridelink`.
3. Upload these 3 files to it (GitHub's web UI lets you drag-and-drop, no git needed).
4. Repo Settings → Pages → Deploy from branch → `main` → `/ (root)`.
5. GitHub gives you a URL like `https://yourname.github.io/ridelink/`.

(Netlify Drop — netlify.com/drop — works too: drag the folder in, get an HTTPS URL instantly.)

## Step 2 — Load it once on both phones

On **both** iPhones, open that HTTPS URL in Safari once while you have normal internet
(home WiFi is fine). Then tap the Share icon → **Add to Home Screen**. This installs the
app shell into the service worker cache so it opens instantly with no internet from then on.

## Step 3 — On the ride, no internet needed

1. One rider turns on **Personal Hotspot** (Settings → Personal Hotspot).
2. The other rider joins that hotspot's WiFi network.
3. Both open the **Ridelink** icon from their home screen.
4. One taps **Start a call**, the other taps **Join a call**.
5. The "host" gets a QR code on screen — the "joiner" taps **Scan call code** and points
   their camera at it. No typing needed, and it works fully offline (the QR encoding/
   decoding happens entirely on-device).
6. This generates a reply QR code on the joiner's phone — the host taps **Scan reply
   code** and scans it back. Connection completes automatically.
7. Audio starts flowing directly between the two phones over the hotspot's local WiFi.
   Neither phone needs cell signal from this point on.

**If scanning is awkward mid-ride** (gloves, glare, one-handed), tap **Show as text code
instead** on either screen to reveal the same code as text, with a **Share via AirDrop**
button and a paste box — this works exactly as before, no camera needed.

Route your helmet Bluetooth headset's mic/speaker to the phone as normal (System audio
settings, or however your headset already routes calls) — the browser tab just needs to
be the active audio session.

## If you already hosted an earlier version

This update adds QR pairing, so `index.html` changed. Re-upload the new file to wherever
you hosted it (GitHub Pages / Netlify), then on both phones: open the site once while on
normal WiFi (not just the cached home-screen icon) so the service worker picks up the new
version, then re-add to Home Screen if needed.

## Notes / limitations

- This is a genuine two-party link, not a group call — it's built for a riding pair.
- QR scanning needs camera permission the first time; the encode/decode both happen
  on-device (no server, no internet), using two small open-source JS libraries bundled
  directly into `index.html` so the app stays fully offline-capable.
- No STUN/TURN server is configured on purpose, since it's designed for two devices on
  the same local network (the hotspot). That's what keeps it internet-free after setup.
- If you back out of the app or your phone locks, iOS may suspend the mic/audio session
  in the background — keep the phone unlocked or screen on during the ride for the most
  reliable connection, same as any browser-based calling.
- If you'd rather skip hosting altogether, check whether your helmet Bluetooth units
  have a built-in intercom mode (Cardo, Sena, etc.) — that bypasses phones entirely and
  is generally the more robust option for riding.
