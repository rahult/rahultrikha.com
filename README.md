# rahultrikha.com

The front door for Rahul Trikha's corner of the internet. A single hand-built page —
no framework, no build step, no trackers — that introduces you and links out to the
projects living on your subdomains:

| Site | What it is |
| --- | --- |
| [blog.rahultrikha.com](https://blog.rahultrikha.com/) | *Lazily Evaluated Thoughts* — the blog (moved off the root domain) |
| [tower.rahultrikha.com](https://tower.rahultrikha.com/) | Local-only control board for supervising coding agents |
| [keg.rahultrikha.com](https://keg.rahultrikha.com/) | Native container management for Apple Silicon Macs |
| [folio.rahultrikha.com](https://folio.rahultrikha.com/) | The review gate for agent-written Markdown |
| [remembero.rahultrikha.com](https://remembero.rahultrikha.com/) | Durable, proof-carrying memory for AI agents |

## Files

```
index.html    the whole page
styles.css    design system (colors + fonts are CSS variables at the top)
script.js     theme toggle + reveal-on-scroll (page works fine without JS)
favicon.svg   RT monogram
404.html      custom not-found page
robots.txt / sitemap.xml
```

## Preview locally

No build step. Either open `index.html` in a browser, or:

```sh
python3 -m http.server 8000
# → http://localhost:8000
```

Append `?theme=dark` or `?theme=light` to a URL to preview a theme without
touching your saved preference.

## Common edits

**Add a project** — open `index.html`, find the `To add a project` comment inside
the projects grid, copy one `<a class="card">` block, and edit the link, title,
URL, description, tags, and the small SVG glyph.

**Change colors** — edit the CSS variables at the top of `styles.css`
(`--accent` is the single brand color; the light and dark palettes are separate
variable blocks).

**Add a photo** — drop an image (e.g. `photo.jpg`) into this folder and uncomment
the `<img class="avatar">` line in the hero section of `index.html`.

**Blog RSS link** — the Writing section links to
`https://blog.rahultrikha.com/atom.xml`. If the new blog home uses `rss.xml` or
`feed.xml` instead, update the href there.

## Deployment & DNS

**Hosting**: GitHub Pages from this repo (`rahult/rahultrikha.com`, branch `main`,
deploy from root). The `CNAME` file pins the custom domain `rahultrikha.com`.

**Current setup** (as of Sept 2026): the `rahultrikha.com` zone is on Cloudflare,
and the apex currently proxies/redirects to `rahult.github.io`, where the Octopress
blog lives (repo `rahult/rahult.github.io`, branch `master`, no custom domain).
`blog.rahultrikha.com` does not resolve yet.

**Cutover order** (keeps the blog reachable throughout):

1. **Cloudflare DNS** — point the apex at GitHub Pages and drop the old redirect:
   - `A` `@` → `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
     (or a flattened `CNAME` `@` → `rahult.github.io.` — Cloudflare supports this at the apex)
   - `CNAME` `www` → `rahult.github.io.`
   - Delete/disable the existing redirect or origin rule that sends `rahultrikha.com`
     to `rahult.github.io` (this is what serves the blog at the apex today).
   - If the records stay proxied (orange cloud), set SSL/TLS mode to **Full** —
     never Flexible, or you'll get redirect loops with Pages' HTTPS enforcement.
     Grey-clouding the records (DNS only) until GitHub issues its certificate also works.
2. **GitHub** — this repo → Settings → Pages: custom domain should show
   `rahultrikha.com` (from the CNAME file); once DNS resolves and the certificate
   is issued, tick **Enforce HTTPS**.
3. **Blog move** — in `rahult/rahult.github.io` → Settings → Pages, set the custom
   domain to `blog.rahultrikha.com` and add a `CNAME` file containing
   `blog.rahultrikha.com` to that repo. Then in Cloudflare add:
   - `CNAME` `blog` → `rahult.github.io.`
4. **Recommended** — verify the domain (github.com Settings → Pages → verified
   domains, TXT record `_github-pages-challenge-rahult`) so it can't be taken over.

Existing subdomains (`tower`, `keg`, `folio`, `remembero`) are untouched.

## Ideas for later

- Add an `og-image.png` (1200×630) and reference it in the `<head>` meta tags.
- If the blog stays static, a **Latest writing** section here could be generated
  from its RSS feed at build time — a reason to graduate to Astro later, but
  this page deliberately stays dependency-free until then.
