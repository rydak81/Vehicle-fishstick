# Deployment Guide

Step-by-step for getting this tool from your laptop to a live URL.

---

## What you need

- A [GitHub](https://github.com) account (free)
- A [Vercel](https://vercel.com) account (free, sign in with GitHub — easiest path)
- Git installed on your computer ([download here](https://git-scm.com/downloads) if you don't have it)

If you've done your Marketplace Beta deployments before, this is the same flow.

---

## Path A: GitHub web UI (easiest, no command line)

### 1. Create the GitHub repo

1. Go to [github.com/new](https://github.com/new)
2. Repository name: `vehicle-decision-engine` (or whatever you prefer)
3. Description: "Buy vs lease comparison tool with SC tax calibration"
4. **Private** or **Public** — your call. Public is fine since there's nothing sensitive in here.
5. **Don't** initialize with README/license (we already have those)
6. Click **Create repository**

### 2. Upload the files

1. On the new empty repo page, click **uploading an existing file** (it's a link in the middle of the page)
2. Drag and drop these files from the folder I provided:
   - `index.html`
   - `package.json`
   - `vercel.json`
   - `README.md`
   - `LICENSE`
   - `.gitignore`
3. Commit message: "Initial commit"
4. Click **Commit changes**

### 3. Deploy to Vercel

1. Go to [vercel.com/new](https://vercel.com/new)
2. If first time, click **Continue with GitHub** and authorize
3. Find `vehicle-decision-engine` in the list, click **Import**
4. **Don't change any settings.** Vercel will auto-detect this as a static site.
5. Click **Deploy**
6. Wait ~30 seconds. You'll get a URL like `vehicle-decision-engine-xyz.vercel.app`

Bookmark that URL on your phone. You're done.

---

## Path B: Command line (faster for future updates)

If you have Git installed and Terminal-comfortable:

```bash
# 1. Move into the folder I provided
cd /path/to/vehicle-decision-engine

# 2. Initialize git
git init
git add .
git commit -m "Initial commit"

# 3. Connect to GitHub (create the empty repo on github.com first, then:)
git remote add origin https://github.com/YOUR_USERNAME/vehicle-decision-engine.git
git branch -M main
git push -u origin main

# 4. Deploy to Vercel
npm install -g vercel
vercel
# Follow the prompts. Choose your account, accept defaults.
```

---

## Custom domain (optional)

You can point a custom domain at Vercel for free.

1. In Vercel dashboard → your project → **Settings** → **Domains**
2. Add your domain (e.g., `tool.beaconpath.com`)
3. Vercel shows you DNS records to add at your registrar
4. Add the records, wait 5–60 minutes for DNS propagation
5. SSL cert auto-provisions

---

## Updating the tool later

After the first deploy, updates are trivial:

```bash
# Edit index.html (or any file)
git add .
git commit -m "Description of what changed"
git push
```

Vercel sees the GitHub push and auto-deploys in ~30 seconds. No manual deploy step needed.

If you used the web UI to upload, you can use GitHub's edit-in-browser feature on `index.html` directly — same auto-deploy will trigger.

---

## Testing checklist after deployment

Once your live URL is up, verify these work:

- [ ] **Page loads** — no console errors (F12 → Console tab)
- [ ] **Vehicle search works** — type "F-150" in either search box, see results
- [ ] **VIN decode works** — click VIN button, paste `1FTFW5L85TKD19247`, should decode to 2026 Ford F-150 Lariat (this confirms NHTSA API is reachable from your live site)
- [ ] **Save scenarios works** — save a scenario, see it appear in the saved bar
- [ ] **Mobile works** — open the URL on your phone, verify everything's tappable

If VIN decode still fails on the live site, check your browser's Network tab for the actual error. CORS shouldn't be an issue (NHTSA allows all origins), but corporate firewalls or VPNs occasionally block government API endpoints.

---

## Troubleshooting

**"Build failed"** — Static HTML shouldn't trigger any build. Verify `vercel.json` exists and `package.json` has no `build` script.

**"404 Not Found"** — Make sure the file is named `index.html` exactly (lowercase, with extension). Vercel serves `/` from `index.html` by default.

**"VIN decode 'Failed to fetch' on live site"** — Open browser dev tools (F12) → Network tab → click VIN decode → look at the request to `vpic.nhtsa.dot.gov`. If it shows CORS error, network firewall is blocking. If it shows status 0 or pending forever, you're offline. The offline WMI decoder will still work as fallback.

**"Charts not rendering"** — Chart.js loads from CDN. If your network blocks `cdnjs.cloudflare.com`, charts won't appear. You can self-host Chart.js by downloading it and changing the script tag.
