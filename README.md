# ZetaServ - File Hash Generator & Verifier

[![Live](https://img.shields.io/badge/live-zetaserv.site-2563EB?style=flat-square)](https://zetaserv.site)
![100% client-side](https://img.shields.io/badge/100%25_client--side-no_uploads-15803D?style=flat-square)
![Algorithms](https://img.shields.io/badge/hashes-MD5_%7C_SHA--1_%7C_SHA--256_%7C_SHA--512-1D4ED8?style=flat-square)
![Free forever](https://img.shields.io/badge/license-free_forever-55637E?style=flat-square)

**ZetaServ** is a free, zero-backend web utility that computes **MD5, SHA-1, SHA-256 and SHA-512**
hashes for any file, verifies them against known values, and produces a **printable, forensic-ready
PDF integrity report** — built for digital evidence work, download verification and everyday
file-integrity checks.

Everything runs in the visitor's browser. **No file is ever uploaded, stored or logged.**

**Try it now: [zetaserv.site](https://zetaserv.site)**

## Highlights

| | |
|---|---|
| **All four algorithms, one pass** | MD5 (RFC 1321) plus SHA-1, SHA-256 and SHA-512 (FIPS 180-4), computed together in a single streamed read of the file |
| **Instant verification** | Paste the expected hash — the algorithm is auto-detected from its length — and get a clear MATCH / MISMATCH verdict |
| **Smart paste parsing** | Accepts plain hex, uppercase, grouped (`ab cd` / `ab:cd`), `sha256sum` output, BSD `md5` output and Windows `certutil` output |
| **Forensic-ready reports** | On-screen report sheet plus PDF download with case/evidence reference, timestamps and chain-of-custody signature lines |
| **Clear confirmations** | A file probe-read on selection gives an explicit "loaded successfully" (green state) or a precise failure reason (unreadable, folder dropped, file moved) |
| **Big-file friendly** | Hashing is streamed in 4 MB chunks with a progress bar and ETA; memory stays flat regardless of file size |
| **Private by design** | No uploads, no cookies, no accounts, no trackers (optional Google Analytics ships disabled) |
| **Works offline** | All libraries are bundled locally — once the page is loaded it needs no network |
| **Light + dark theme** | Respects your OS preference; remembers your choice |
| **No build step** | Plain HTML, CSS and JavaScript — nothing to compile, nothing to install |

## Built for digital evidence work

Hashing is the foundation of digital evidence integrity: a digest proves that a file has not
changed by a single bit between acquisition and presentation. ZetaServ supports that workflow
end to end:

- **At acquisition** — hash the evidence item and record the digests immediately.
- **At every hand-off** — re-hash and verify against the recorded values in seconds.
- **For the record** — attach the PDF report, with case reference and signature lines, to your
  chain-of-custody file.

The built-in [guide](https://zetaserv.site/guide.html) explains hashing from first principles,
compares the algorithms (including why MD5 and SHA-1 are legacy but still required by many
evidence workflows), walks through the verification process, and covers evidence preservation
and chain-of-custody best practices.

## How it works

```
your file ──► File API (chunked 4 MB reads) ──► incremental hashers ──► digests
                    │                                                  │
                    └── never leaves the browser ◄──────────────────────┘
```

- **MD5** — [SparkMD5](https://github.com/satazor/js-spark-md5) (pure JavaScript, incremental)
- **SHA-1 / SHA-256 / SHA-512** — [jsSHA](https://github.com/Caligatio/jsSHA) (incremental)
- **PDF report** — [jsPDF](https://github.com/parallax/jsPDF), generated client-side
- All four digests are computed in **one pass** over the data, so selecting more algorithms
  costs almost no extra read time.

Digests were verified byte-identical against coreutils `md5sum`, `sha1sum`, `sha256sum` and
`sha512sum` on multi-megabyte reference files during development, and cross-checked on
Windows with `certutil -hashfile`.

## Run it locally

No tooling required — either just open the page:

```bash
# simply open index.html in any modern browser
```

or serve it (recommended, so all browsers behave exactly like production):

```bash
cd zetaserv-repo
python3 -m http.server 8080     # then visit http://localhost:8080
```

## Deploy it

ZetaServ is plain static files — it runs on **any** static host:

| Host | Notes |
|---|---|
| **Apache / cPanel shared hosting** (BigRock, etc.) | Upload the files to `public_html`. The included `.htaccess` adds compression, cache rules and hardening headers. See `README-DEPLOY.txt`. |
| **GitHub Pages** | Push this repo and enable Pages. Note: GitHub Pages ignores `.htaccess`, so the security headers simply won't be set — everything else works identically. |
| **Netlify / Cloudflare Pages / Vercel** | Drag-and-drop or connect the repo. Add the same headers via the host's config if you want them. |

Enable HTTPS on whichever host you choose (free AutoSSL/Let's Encrypt on cPanel).

## Project structure

```
├── index.html            # The hash tool (generate, verify, report)
├── guide.html            # Hashing & digital-evidence guide (8 sections + FAQ)
├── privacy.html          # Privacy policy + disclaimer
├── css/style.css         # Design system: light + dark themes, print layout
├── js/
│   ├── app.js            # UI logic: selection, confirmations, progress, verify
│   ├── hashers.js        # Incremental multi-algorithm hashing core
│   ├── report.js         # On-screen report sheet + jsPDF builder
│   ├── utils.js          # Shared formatting helpers
│   ├── ga-init.js        # Optional Google Analytics bootstrap (disabled)
│   └── lib/              # Bundled third-party libraries (see below)
├── img/                  # Logo, favicons, social share image
├── fonts/                # Inter (woff2, self-hosted)
├── docs/                 # Screenshots used in this README
├── .htaccess             # Apache: caching, compression, security headers, CSP
├── robots.txt
└── sitemap.xml
```

## Security posture

- **No attack surface for file abuse.** Files are read as bytes by the visitor's own browser
  and never transmitted. There is no upload endpoint, no form handler, no API and no database —
  which is also why no CAPTCHA / Cloudflare Turnstile is needed: those protect *server*
  endpoints, and this site has none.
- **Strict Content-Security-Policy** (`default-src 'self'`), `X-Frame-Options`,
  `Referrer-Policy`, `Permissions-Policy` and `nosniff` via `.htaccess`.
- **Zero third-party requests** in the default build — all libraries and fonts are bundled,
  so there is no CDN dependency to tamper with.
- Malicious files can do no harm: they are only *read* as bytes, never executed.

## Credits and third-party licenses

| Component | Version | Purpose | License |
|---|---|---|---|
| [SparkMD5](https://github.com/satazor/js-spark-md5) | 3.0.2 | MD5 | MIT |
| [jsSHA](https://github.com/Caligatio/jsSHA) | 3.3.0 | SHA-1 / SHA-256 / SHA-512 | BSD-3-Clause |
| [jsPDF](https://github.com/parallax/jsPDF) | 2.5.2 | PDF report generation | MIT |
| [Inter](https://rsms.me/inter/) | — | Typography | SIL OFL 1.1 |

License headers are preserved inside `js/lib/`.

## Feedback

Found a bug, or want a feature? Open an issue on this repository or write to
**[hola@zetaserv.site](mailto:hola@zetaserv.site)**.

## More free tools from Tekzilver

| Tool | What it does | Where |
|---|---|---|
| **IPBlockr** | Blocks bad bots, VPNs, proxies, Tor exits, datacenter bots and fake search-engine spiders before they reach your site — one line of code | [ipblockr.cc](https://ipblockr.cc) |
| **ZetaservSEO** | Automated, AI-powered SEO intelligence, audits, content strategy and social distribution workspace | [zetaserv.online](https://zetaserv.online) |
| **PostMortem** | Free forensic log analyzer for Apache, Nginx, CSV and JSON logs — investigate breaches, trace malicious IPs, detect web shells and SQLi/XSS | [GitHub](https://github.com/tekzilver/PostMortem) |
| **IPTrace** | Free standalone forensic IP threat intelligence, geolocation and log analysis suite (Windows) | [GitHub](https://github.com/tekzilver/IPTrace) |
| **SiteMapper** | Fast, unlimited, free-forever XML sitemap generator (Windows) | [GitHub](https://github.com/tekzilver/SiteMapper) |

---

A [Tekzilver](https://github.com/tekzilver) service - free forever, best-effort basis.
