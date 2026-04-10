#   <img src="https://i.postimg.cc/k5McRz7f/icon128.png" width="72" alt="Divine Wisdom Icon" /><br><br> Divine Wisdom: Daily Hindu Scriptures & Spiritual Insights

> Transform your browser with daily verses from the Bhagavad Gita, Puranas, and Vedas. One sacred verse per session — appearing gently, then fading away.

![Chrome Web Store](https://img.shields.io/badge/Chrome%20Web%20Store-Available-brightgreen?logo=google-chrome)
![Manifest Version](https://img.shields.io/badge/Manifest-v3-blue)
![Version](https://img.shields.io/badge/Version-0.3-orange)
---

## 📖 Overview

**Divine Wisdom** is a lightweight Chrome extension that displays one sacred Hindu scripture verse per browser session. After a natural **5–7 second delay**, a beautifully styled overlay slides down from the top of your screen — then quietly fades after 10–20 seconds. No interruptions. No clutter. Just one moment of ancient wisdom per day.

Verses are drawn from the **Bhagavad Gita**, **Puranas**, and **Vedas**, delivered in a premium saffron-and-gold aesthetic designed to feel sacred and calm.

---

## ✨ How It Works

1. You **start or restart your browser**
2. The extension sets a session flag — one message is queued
3. On the **first page you visit**, after a **5–7 second natural delay**, the verse overlay slides in from the top
4. The verse displays for **10–20 seconds**, then gently fades out
5. **No more overlays** appear until the next browser restart

This one-per-session design is intentional — preserving your focus while still delivering a mindful moment.

---

## 🗂️ Project Structure

```
divine-wisdom/
├── manifest.json          # Chrome Extension manifest (v3)
├── background.js          # Service worker — session gate & message logic
├── content.js             # Overlay injection & timing logic
├── popup.html             # Extension popup UI
├── styles.css             # Overlay styles (Cinzel + Poppins fonts, saffron theme)
├── messages.json          # Scripture verses database (5000+ entries)
├── logo_gen.html          # Animated logo generator (Canvas-based Om symbol)
├── icon16.png             # Toolbar icon (16x16)
├── icon48.png             # Extensions page icon (48x48)
├── icon128.png            # Chrome Web Store icon (128x128)
└── README.md              # This file
```

---

## ⚙️ Technical Architecture

### Manifest v3 — Service Worker Model

The extension runs on **Manifest v3** using a service worker (`background.js`) instead of a persistent background page. This is the modern Chrome standard and keeps the extension lightweight and efficient.

### Session Gate (Race-Condition Safe)

The background script uses a **dual-lock system** to guarantee only one verse is ever shown per session — even when multiple tabs open simultaneously:

```
Browser Starts
     │
     ▼
sessionGateLocked = false
showMessage = true  (session storage)
     │
     ▼
First tab sends getMessage
     │
     ├─ Lock already set? → return null (no overlay)
     │
     └─ Not locked → Lock immediately + clear flag → fetch verse → send to tab
```

- **In-memory lock** (`sessionGateLocked`) — blocks concurrent requests in the same service worker instance
- **Session storage flag** (`showMessage`) — persists across service worker restarts within the same session

### Overlay Timing

```
Page loads
    │
    └── Random delay: 5000–7000ms
              │
              └── Request verse from background
                        │
                        └── Slide in (0.8s cubic-bezier spring)
                                  │
                                  └── Random duration: 10000–20000ms
                                            │
                                            └── Fade out + remove from DOM
```

### Permissions — What & Why

| Permission | Used In | Purpose |
|---|---|---|
| `storage` | `background.js` | Session storage for the one-per-session gate flag |
| `tabs` | `manifest.json` | Required to access tab context for content script injection |
| `activeTab` | `manifest.json` | Enables the popup when the extension icon is clicked |

> No browsing data is read, recorded, or transmitted. The extension only writes and reads its own session flag.

---

## 🎨 Design System

### Fonts
- **Cinzel** (serif) — Used for scripture source labels and speaker names. Evokes classical sacred typography.
- **Poppins** (sans-serif) — Used for verse body text. Clean and readable.
- **Outfit** (sans-serif) — Used in the popup UI for a modern, airy feel.

### Color Palette

| Name | Hex | Used For |
|---|---|---|
| Saffron | `#ff9933` | Overlay border, source label, accent |
| Gold | `#d4af37` | Speaker name highlight |
| Deep Amber | `#4a2c00` | Body text on overlay |
| Warm White | `#fffbf5` | Popup background |
| Accent Orange | `#e67e22` | Popup headings and UI accents |

### Overlay Behavior
- Slides in from **top of screen** (off-screen start → `top: 0`)
- Spring easing: `cubic-bezier(0.175, 0.885, 0.32, 1.275)`
- `z-index: 2147483647` — always on top
- `pointer-events: none` — never blocks your clicks
- Subtle **Om** watermark in the bottom-right corner

---

## 📦 Installation

### From Chrome Web Store *(Recommended)*
1. Visit the Chrome Web Store listing *(link coming soon)*
2. Click **Add to Chrome**
3. Confirm the permissions
4. Restart your browser and open any page — your first verse will appear

### Manual / Developer Installation
1. Clone or download this repository
   ```bash
   git clone https://github.com/yourusername/divine-wisdom-extension.git
   ```
2. Open Chrome and go to `chrome://extensions/`
3. Enable **Developer Mode** (toggle, top-right)
4. Click **Load unpacked** and select the project folder
5. Restart Chrome and open any webpage to trigger your first verse

---

## 🛠️ Development Guide

### Testing the Overlay
Because the extension shows only once per session, use this workflow during development:

1. Make your changes
2. Go to `chrome://extensions/` and click the **refresh** icon on the extension card
3. This re-triggers `onInstalled`, which resets the session gate
4. Open any tab — the overlay will appear after 5–7 seconds

### Adding New Verses
Verses are stored in `messages.json` as an array. Each entry follows this structure:

```json
[
  {
    "source": "Bhagavad Gita",
    "chapter": "Chapter 2, Verse 47",
    "speaker": "Lord Krishna",
    "message": "You have a right to perform your prescribed duties, but you are not entitled to the fruits of your actions."
  }
]
```

| Field | Description |
|---|---|
| `source` | Scripture name — displayed in uppercase saffron text |
| `chapter` | Chapter and verse reference — displayed as a subtitle |
| `speaker` | Who speaks the verse — displayed in gold |
| `message` | The verse text — displayed in italic |

### Updating the Logo
`logo_gen.html` is a **Canvas-based animated logo generator** featuring:
- Radial saffron gradient background
- Animated Om symbol with pulsing glow effect
- Color cycling through gold → saffron → deep orange
- Gold outer ring border

Open it in Chrome, screenshot at the required sizes, and save as `icon16.png`, `icon48.png`, and `icon128.png`.

---

## 🔒 Privacy Policy

This extension collects **zero personal data**.

- No browsing history is accessed or stored
- No data is sent to any external server
- All storage is local and limited to the extension's own session flag
- Content scripts only display the overlay — no page content is read or transmitted

Full privacy policy: [DivineWisdom_PrivacyPolicy.docx](./DivineWisdom_PrivacyPolicy.docx)

---

## 📜 Scripture Sources

- **Bhagavad Gita** — 700 verses of dialogue between Arjuna and Lord Krishna on duty, dharma, and liberation
- **Vedas** — The four foundational texts of Hindu philosophy: Rigveda, Samaveda, Yajurveda, Atharvaveda
- **Puranas** — 18 ancient texts covering cosmology, mythology, genealogy, and devotional teachings

---

## 🤝 Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you'd like to change.

1. Fork the repository
2. Create your branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m 'Add some feature'`
4. Push to the branch: `git push origin feature/your-feature`
5. Open a Pull Request

---

## ☕ Support

If this extension brings peace to your day, consider supporting future projects:

👉 [SUPPORT & HELP](https://razorpay.me/@amanxr71)

---

<p align="center">
  <img src="https://i.postimg.cc/k5McRz7f/icon128.png" width="72" alt="Divine Wisdom Icon" /><br><br>
  <i>"One who sees inaction in action, and action in inaction, is intelligent among men."</i><br>
  <b>— Bhagavad Gita</b><br><br>
  Made with 🕉️ and devotion
</p>
