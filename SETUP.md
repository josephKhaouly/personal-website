# Joseph Khaouly — Executive Site · Setup & Maintenance

Everything you'll ever edit lives in **one place**. You never touch HTML.

```
site/
├─ index.html      ← structure + styling + render logic (don't edit)
├─ content.json    ← your data (the schema); mirror of the SITE block
├─ assets/
│  ├─ portrait.png ← your headshot (already dropped in)
│  └─ Joseph-Khaouly-Executive-Dossier.pdf  ← add your CV PDF here
└─ SETUP.md
```

---

## 1. Recommended stack (lowest maintenance)

**Ship it as static files. No build step, no framework, no server.**
`index.html` + `content.json` + `/assets`, hosted on any static host
(**Cloudflare Pages, Netlify, or Vercel** — all free, all give HTTPS +
custom domains + subdomains out of the box). Drag-and-drop or `git push`
to deploy. This is the simplest thing that meets every requirement.

*If you later want Markdown blog posts or many project pages,* graduate to
**Astro + Tailwind** (keep `content.json` as-is, Astro reads it natively).
Not needed today — don't add tooling you don't need.

---

## 2. How to update content

The site reads from the `SITE` object at the bottom of `index.html`, under
the banner `▓▓▓ EDIT ONLY THIS BLOCK ▓▓▓`. Edit values there and refresh.
`content.json` is the same data as a clean file for your reference.

**Common edits**
- **New completed project** → add an object to `projects` with `"status":"done"`.
- **Move "coming soon" → live** → change that project's `"status"` to `"active"`
  and set its real `"url"`.
- **New roadmap item** → add to `roadmap`.
- **Change bio / value prop** → edit `about` / `hero.valueProp`.

**Prefer editing one external file and never opening index.html?**
Rename `content.json` → `content.js`, add `window.SITE =` in front of the
`{`, then in `index.html` delete the inline `const SITE = {…}` and add
`<script src="content.js"></script>` just above the render script.
(We use a JS file, not raw JSON via `fetch`, so it works even when opened
directly from disk — no CORS headaches.)

---

## 3. PDF CV — the download button

Two "Download dossier / résumé (PDF)" buttons are already wired to
`cvUrl` in the data. Just:

1. Drop your PDF in `assets/` (e.g. `Joseph-Khaouly-Executive-Dossier.pdf`).
2. Point `cvUrl` at it. Done.

The buttons use the native download attribute:

```html
<a href="assets/Joseph-Khaouly-Executive-Dossier.pdf" download>
  Download résumé (PDF) ↓
</a>
```

`download` forces a save/download rather than opening in-tab — best for
recruiters. Keep the filename clean; it becomes the saved filename.

---

## 4. Subdomain routing (project1.yourdomain.com)

Each venture is its own deployment on its own subdomain. Pattern:

| What you're pointing        | Record | Host / Name | Value (target)                    |
|-----------------------------|--------|-------------|-----------------------------------|
| Root site (yourdomain.com)  | A / ALIAS | `@`      | your host's IP / target           |
| `www`                       | CNAME  | `www`       | `yourdomain.com`                  |
| **REA** (rea.yourdomain.com)| CNAME  | `rea`       | `rea-project.pages.dev` (its deploy URL) |
| S2S (s2s.yourdomain.com)    | CNAME  | `s2s`       | `s2s-project.pages.dev`           |

**Steps (Cloudflare / Netlify / Vercel are near-identical):**
1. Deploy the project → the host gives you a URL like `rea-xyz.pages.dev`.
2. In your DNS provider, add a **CNAME**: name `rea`, value = that deploy URL.
3. In the host's dashboard, add the **custom domain** `rea.yourdomain.com`
   to that project so it provisions an SSL cert.
4. Wait for DNS to propagate (minutes to ~an hour). HTTPS is automatic.

Notes:
- Use **CNAME** for subdomains pointing at a platform URL; use **A/ALIAS**
  only for the apex/root where CNAME isn't allowed.
- Then set the project's `url` in the data to `https://rea.yourdomain.com`.

---

## 5. Deploy the main site (2 minutes, Cloudflare Pages)

1. Put this `site/` folder in a Git repo (or use drag-and-drop upload).
2. Cloudflare Pages → Create project → connect repo (build command: none,
   output dir: `/`).
3. Add custom domain `yourdomain.com` → follow the DNS prompt.
4. Every future edit = change `content.json` / the SITE block → push → live.
