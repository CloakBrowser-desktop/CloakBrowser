<p align="center">
<img src="https://i.imgur.com/cqkp6fG.png" width="500" alt="CloakBrowser">
</p>

<h3 align="center">Stealth Chromium that passes every bot detection test.</h3>

<table><tr><td>
Not a patched config. Not a JS injection. A real Chromium binary with fingerprints modified at the C++ source level. Antibot systems score it as a normal browser — because it <em>is</em> a normal browser.
</td></tr></table>

<br>

<p align="center">
<img src="https://i.imgur.com/IvB0It7.gif" width="600" alt="Cloudflare Turnstile — 3 Tests Passing">
<br><em>Cloudflare Turnstile — 3 live tests passing (headed mode, macOS)</em>
</p>

<br>

<p align="center">
Drop-in Playwright/Puppeteer replacement for Python and JavaScript.<br>
Same API, same code — just swap the import. <strong>3 lines of code, 30 seconds to unblock.</strong>
</p>

- **49 source-level C++ patches** — canvas, WebGL, audio, fonts, GPU, screen, WebRTC, network timing, automation signals, CDP input behavior
- **`humanize=True`** — human-like mouse curves, keyboard timing, and scroll patterns. One flag, behavioral detection passes
- **0.9 reCAPTCHA v3 score** — human-level, server-verified
- **Passes Cloudflare Turnstile**, FingerprintJS, BrowserScan — tested against 30+ detection sites
- **Auto-updating binary** — background update checks, always on the latest stealth build
- **`pip install cloakbrowser`** or **`npm install cloakbrowser`** — binary auto-downloads, zero config
- **Free and open source** — no subscriptions, no usage limits

## Install

Download the latest build from the [Releases](../../releases) page.

| Platform       | File                    |
| -------------- | ----------------------- |
| macOS          | `.dmg`                  |
| Linux (any)    | `.AppImage`             |
| Windows        | `.exe`                  |

## Browser Profile Manager

Self-hosted alternative to Multilogin, GoLogin, and AdsPower. Create browser profiles with unique fingerprints, proxies, and persistent sessions. Launch and interact with them in your browser via noVNC.

Open [http://localhost:8080](http://localhost:8080). Create a profile. Click **Launch**. Done.

---

## Latest: v0.3.26 (Chromium 146.0.7680.177.4)

- **`launch_context_async()`** — async counterpart to `launch_context()`. Forwards kwargs to `browser.new_context()` for `storage_state`, `permissions`, `extra_http_headers` without a persistent profile folder.
- **JS `contextOptions` escape hatch** — forward arbitrary options (including `storageState`) to Playwright's `newContext()` from `launchContext()` / `launchPersistentContext()`.
- **Native SOCKS5 proxy** — `proxy="socks5://user:pass@host:port"` works directly in all launch functions, Python + JS. QUIC/HTTP3 tunnels through SOCKS5 via UDP ASSOCIATE.
- **Chromium 146 upgrade** — rebased all patches from 145.0.7632.x to 146.0.7680.177
- **57 fingerprint patches** — additional detection-vector coverage (WebAuthn, AAC audio, window position) and WebGL/canvas consistency fixes
- **WebRTC IP spoofing** — `--fingerprint-webrtc-ip=auto` resolves your proxy's exit IP and spoofs WebRTC ICE candidates. Auto-injected when using `geoip=True` (no extra network call)
- **Proxy signal removal** — DNS/connect/SSL timing zeroed, proxy cache headers stripped, Proxy-Connection header leak removed
- **`cloakserve` CDP multiplexer** — rewritten as a multi-connection CDP proxy with per-connection fingerprint seeds
- **Humanize CDP isolation** — keyboard events now use isolated worlds and trusted dispatch for better behavioral stealth
- **`humanize=True`** — one flag makes all mouse, keyboard, and scroll interactions behave like a real user. Bézier curves, per-character typing, realistic scroll patterns
- **Stealthy with zero flags** — binary auto-generates a random fingerprint seed at startup. No configuration required
- **Timezone & locale from proxy IP** — `launch(proxy="...", geoip=True)` auto-detects timezone and locale
- **Persistent profiles** — `launch_persistent_context()` keeps cookies and localStorage across sessions, bypasses incognito detection

## Why CloakBrowser?

- **Config-level patches break** — `playwright-stealth`, `undetected-chromedriver`, and `puppeteer-extra` inject JavaScript or tweak flags. Every Chrome update breaks them. Antibot systems detect the patches themselves.
- **CloakBrowser patches Chromium source code** — fingerprints are modified at the C++ level, compiled into the binary. Detection sites see a real browser because it *is* a real browser.
- **Source-level stealth** — C++ patches handle fingerprints (GPU, screen, UA, hardware reporting) at the binary level. No JavaScript injection, no config-level hacks. Most stealth tools only patch at the surface.
- **Same behavior everywhere** — works identically local, in Docker, and on VPS. No environment-specific patches or config needed.
- **Works with AI agents and automation frameworks** — drop-in stealth for browser-use, Crawl4AI, Scrapling, Stagehand, LangChain, Selenium, and more. See [integrations](#framework-integrations).

CloakBrowser doesn't solve CAPTCHAs — it prevents them from appearing. No CAPTCHA-solving services, no proxy rotation built in — bring your own proxies, use the Playwright API you already know.

## Test Results

All tests verified against live detection services. Last tested: Apr 2026 (Chromium 146).

| Detection Service | Stock Playwright | CloakBrowser | Notes |
|---|---|---|---|
| **reCAPTCHA v3** | 0.1 (bot) | **0.9** (human) | Server-side verified |
| **Cloudflare Turnstile** (non-interactive) | FAIL | **PASS** | Auto-resolve |
| **Cloudflare Turnstile** (managed) | FAIL | **PASS** | Single click |
| **ShieldSquare** | BLOCKED | **PASS** | Production site |
| **FingerprintJS** bot detection | DETECTED | **PASS** | demo.fingerprint.com |
| **BrowserScan** bot detection | DETECTED | **NORMAL** (4/4) | browserscan.net |
| **bot.incolumitas.com** | 13 fails | **1 fail** | WEBDRIVER spec only |
| **deviceandbrowserinfo.com** | 6 true flags | **0 true flags** | `isBot: false` |
| `navigator.webdriver` | `true` | **`false`** | Source-level patch |
| `navigator.plugins.length` | 0 | **5** | Real plugin list |
| `window.chrome` | `undefined` | **`object`** | Present like real Chrome |
| UA string | `HeadlessChrome` | **`Chrome/146.0.0.0`** | No headless leak |
| CDP detection | Detected | **Not detected** | `isAutomatedWithCDP: false` |
| TLS fingerprint | Mismatch | **Identical to Chrome** | ja3n/ja4/akamai match |
| | | **Tested against 30+ detection sites** | |

### Proof

<p align="center">
<img src="https://i.imgur.com/hvIQyMv.png" width="600" alt="reCAPTCHA v3 — Score 0.9">
<br><em>reCAPTCHA v3 score 0.9 — server-side verified (human-level)</em>
</p>

<p align="center">
<img src="https://i.imgur.com/qMIRfhq.png" width="600" alt="Cloudflare Turnstile — Success">
<br><em>Cloudflare Turnstile non-interactive challenge — auto-resolved</em>
</p>

<p align="center">
<img src="https://i.imgur.com/PRsw6rT.png" width="600" alt="BrowserScan — Normal">
<br><em>BrowserScan bot detection — NORMAL (4/4 checks passed)</em>
</p>

<p align="center">
<img src="https://i.imgur.com/9n2C7tu.png" width="600" alt="FingerprintJS — Passed">
<br><em>FingerprintJS web-scraping demo — data served, not blocked</em>
</p>

<p align="center">
<img src="https://i.imgur.com/srCcFtK.png" width="600" alt="deviceandbrowserinfo.com — You are human!">
<br><em>deviceandbrowserinfo.com behavioral bot detection — "You are human!" with humanize=True (24/24 signals passed)</em>
</p>

## Comparison

| Feature | Playwright | playwright-stealth | undetected-chromedriver | Camoufox | CloakBrowser |
|---|---|---|---|---|---|
| reCAPTCHA v3 score | 0.1 | 0.3-0.5 | 0.3-0.7 | 0.7-0.9 | **0.9** |
| Cloudflare Turnstile | Fail | Sometimes | Sometimes | Pass | **Pass** |
| Patch level | None | JS injection | Config patches | C++ (Firefox) | **C++ (Chromium)** |
| Survives Chrome updates | N/A | Breaks often | Breaks often | Yes | **Yes** |
| Maintained | Yes | Stale | Stale | Unstable | **Active** |
| Browser engine | Chromium | Chromium | Chrome | Firefox | **Chromium** |
| Playwright API | Native | Native | No (Selenium) | No | **Native** |

## How It Works

CloakBrowser is a thin wrapper (Python + JavaScript) around a custom-built Chromium binary:

1. **You install** → `pip install cloakbrowser` or `npm install cloakbrowser`
2. **First launch** → binary auto-downloads for your platform (Chromium 146)
3. **Every launch** → Playwright or Puppeteer starts with our binary + stealth args
4. **You write code** → standard Playwright/Puppeteer API, nothing new to learn

The binary includes 49 source-level patches covering canvas, WebGL, audio, fonts, GPU, screen properties, WebRTC, network timing, hardware reporting, automation signal removal, and CDP input behavior mimicking.

These are compiled into the Chromium binary — not injected via JavaScript, not set via flags.

Binary downloads are verified with SHA-256 checksums to ensure integrity.

## Configuration

| Env Variable | Default | Description |
|---|---|---|
| `CLOAKBROWSER_BINARY_PATH` | — | Skip download, use a local Chromium binary |
| `CLOAKBROWSER_CACHE_DIR` | `~/.cloakbrowser` | Binary cache directory |
| `CLOAKBROWSER_DOWNLOAD_URL` | `cloakbrowser.dev` | Custom download URL for binary |
| `CLOAKBROWSER_AUTO_UPDATE` | `true` | Set to `false` to disable background update checks |
| `CLOAKBROWSER_SKIP_CHECKSUM` | `false` | Set to `true` to skip SHA-256 verification after download |
| `CLOAKBROWSER_GEOIP_TIMEOUT_SECONDS` | `5` | Max seconds for GeoIP resolution before continuing without it |

## Fingerprint Management

The binary is **stealthy by default** — no flags needed. It auto-generates a random fingerprint seed at startup and spoofs all detectable values (GPU, hardware specs, screen dimensions, canvas, WebGL, audio, fonts). Every launch produces a fresh, coherent identity.

**How fingerprinting works:**

| Scenario | What happens |
|----------|-------------|
| **No flags** | Random seed auto-generated at startup. GPU, screen, hardware specs, and all noise patches are spoofed automatically. Fresh identity each launch. |
| **`--fingerprint=seed`** | Deterministic identity from the seed. Same seed = same fingerprint across launches. Use this for session persistence (returning visitor). |
| **`--fingerprint=seed` + explicit flags** | Explicit flags override individual auto-generated values. The seed fills in everything else. |

The binary detects its platform at compile time — a macOS binary reports as macOS with Apple GPU, a Linux binary reports as Linux with NVIDIA GPU. The **wrapper** overrides this on Linux by passing `--fingerprint-platform=windows`, so sessions appear as Windows desktops (more common fingerprint, harder to cluster). Use `--fingerprint-platform` for cross-platform spoofing when running the binary directly.

> **Tip: Use a fixed seed when revisiting the same site.** A random seed makes every session look like a different device — which can be suspicious when hitting the same site repeatedly from the same IP. For reCAPTCHA v3 Enterprise and similar scoring systems, a fixed seed produces a consistent fingerprint across sessions, making you look like a returning visitor:
> ```python
> browser = launch(args=["--fingerprint=12345"])
> ```
> ```javascript
> const browser = await launch({ args: ['--fingerprint=12345'] });
> ```

### Default Fingerprint

Every `launch()` call sets these automatically. The **wrapper** applies platform-aware defaults — on Linux it spoofs as Windows for a more common fingerprint, on macOS it runs as a native Mac browser:

| Flag | Linux/Windows Default | macOS Default | Controls |
|------|--------------|---------------|----------|
| `--fingerprint` | Random (10000–99999) | Random (10000–99999) | Master seed for canvas, WebGL, audio, fonts, client rects |
| `--fingerprint-platform` | `windows` | `macos` | `navigator.platform`, User-Agent OS, GPU pool selection |

The binary auto-generates everything else from the seed: GPU, hardware concurrency, device memory, and screen dimensions. Each seed produces a unique, consistent fingerprint. Override with explicit flags if needed.

> **Using the binary directly?** It works out of the box with zero flags -- the binary auto-spoofs everything. Pass `--fingerprint=seed` for a persistent identity, or use explicit flags like `--fingerprint-gpu-renderer` to override any auto-generated value.

### Additional Flags

Supported by the binary but **not set by default** — pass via `args` to customize:

| Flag | Controls |
|------|----------|
| `--fingerprint-gpu-vendor` | WebGL `UNMASKED_VENDOR_WEBGL` (auto-generated from seed + platform) |
| `--fingerprint-gpu-renderer` | WebGL `UNMASKED_RENDERER_WEBGL` (auto-generated from seed + platform) |
| `--fingerprint-hardware-concurrency` | `navigator.hardwareConcurrency` (auto-generated: `8`) |
| `--fingerprint-device-memory` | `navigator.deviceMemory` in GB (auto-generated: `8`) |
| `--fingerprint-screen-width` | Screen width (auto-generated: `1920` Win/Linux, `1440` macOS) |
| `--fingerprint-screen-height` | Screen height (auto-generated: `1080` Win/Linux, `900` macOS) |
| `--fingerprint-brand` | Browser brand: `Chrome`, `Edge`, `Opera`, `Vivaldi` |
| `--fingerprint-brand-version` | Brand version (UA + Client Hints) |
| `--fingerprint-platform-version` | Client Hints platform version |
| `--fingerprint-location` | Geolocation coordinates |
| `--fingerprint-timezone` | Timezone (e.g. `America/New_York`) |
| `--fingerprint-locale` | Locale (e.g. `en-US`) |
| `--fingerprint-storage-quota` | Override storage quota in MB — affects `storage.estimate()`, `storageBuckets`, and legacy webkit APIs. Auto-normalized when `--fingerprint` is set |
| `--fingerprint-taskbar-height` | Override taskbar height (binary defaults: Win=48, Mac=95, Linux=0) |
| `--fingerprint-fonts-dir` | Path to directory containing target-platform fonts |
| `--fingerprint-webrtc-ip` | WebRTC ICE candidate IP replacement. Use `auto` to resolve from proxy exit IP (makes an HTTP call through the proxy), or pass an explicit IP. Auto-injected when `geoip=True` |
| `--fingerprint-noise=false` | Disable noise injection (canvas, WebGL, audio, client rects) while keeping the deterministic fingerprint seed active |
| `--enable-blink-features=FakeShadowRoot` | Access closed shadow DOM elements |

> **Note:** All stealth tests were verified with the default fingerprint config above. Changing these flags may affect detection results — test your configuration before using in production.


## Platforms

| Platform | Chromium | Patches | Status |
|---|---|---|---|
| Linux x86_64 | 146 | 57 | ✅ Latest |
| Linux arm64 (RPi, Graviton) | 146 | 57 | ✅ Latest |
| macOS arm64 (Apple Silicon) | 145 | 26 | ✅ |
| macOS x86_64 (Intel) | 145 | 26 | ✅ |
| Windows x86_64 | 146 | 57 | ✅ Latest |

The wrapper auto-downloads the correct binary for your platform.

**macOS first launch:** The binary is ad-hoc signed. On first run, macOS Gatekeeper will block it. Right-click the app → **Open** → click **Open** in the dialog. This is only needed once.

## Troubleshooting

---
### reCAPTCHA v3 scores are low (0.1–0.3)

Avoid `page.wait_for_timeout()` — it sends CDP protocol commands that reCAPTCHA detects. Use native sleep instead:

```python
# Bad — sends CDP commands, reCAPTCHA detects this
page.wait_for_timeout(3000)

# Good — invisible to the browser
import time
time.sleep(3)
```

```javascript
// Bad — sends CDP commands
await page.waitForTimeout(3000);

// Good — invisible to the browser
await new Promise(r => setTimeout(r, 3000));
```

Other tips for maximizing reCAPTCHA scores:
- **Try the Patchright backend** — suppresses additional CDP automation signals at the Playwright protocol layer. Install with `pip install cloakbrowser[patchright]`, then use `launch(backend="patchright")` or set `CLOAKBROWSER_BACKEND=patchright` globally. Note: Patchright breaks proxy auth and `add_init_script` — only use it if you're still seeing low scores after trying the steps above
- **Use Playwright, not Puppeteer** — Puppeteer sends more CDP protocol traffic that reCAPTCHA detects
- **Use residential proxies** — datacenter IPs are flagged by IP reputation, not browser fingerprint
- **Spend 15+ seconds on the page** before triggering reCAPTCHA — short visits score lower
- **Space out requests** — back-to-back `grecaptcha.execute()` calls from the same session get penalized. Wait 30+ seconds between pages with reCAPTCHA
- **Use a fixed fingerprint seed** for consistent device identity across sessions
- **Use `page.type()` instead of `page.fill()`** for form filling — `fill()` sets values directly without keyboard events, which reCAPTCHA's behavioral analysis flags. `type()` with a delay simulates real keystrokes:
  ```python
  page.type("#email", "user@example.com", delay=50)
  ```
- **Minimize `page.evaluate()` calls** before the reCAPTCHA check fires — each one sends CDP traffic

## FAQ

**Q: Is this legal?**
A: CloakBrowser is a browser built on open-source Chromium. We do not condone illegal use. Automating systems without authorization, credential stuffing, and account creation abuse are expressly prohibited.

**Q: How is this different from Camoufox?**
A: Camoufox patches Firefox. We patch Chromium. Chromium means native Playwright support, larger ecosystem, and TLS fingerprints that match real Chrome. Camoufox returned in early 2026 but is in unstable beta — CloakBrowser is production-ready.

**Q: Will detection sites eventually catch this?**
A: Possibly. Bot detection is an arms race. Source-level patches are harder to detect than config-level patches, but not impossible. We actively monitor and update when detection evolves.

**Q: Can I use my own proxy?**
A: Yes. Pass `proxy="http://user:pass@host:port"` or `proxy="socks5://user:pass@host:port"` to `launch()`. Both HTTP and SOCKS5 proxies are supported natively.

## Roadmap

| Feature | Status |
|---------|--------|
| Linux x64 — Chromium 146 (57 patches) | ✅ Released |
| macOS arm64/x64 — Chromium 145 (26 patches) | ✅ Released |
| Windows x64 — Chromium 146 (57 patches) | ✅ Released |
| JavaScript/Puppeteer + Playwright support | ✅ Released |
| Fingerprint rotation per session | ✅ Released |
| Built-in proxy rotation | 📋 Planned |

## License

- **CloakBrowser binary** (compiled Chromium) — free to use, no redistribution. See [LICENSE.md](LICENSE.md).
