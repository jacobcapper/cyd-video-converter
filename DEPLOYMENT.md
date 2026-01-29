# Deployment Guide for CYD Video Converter

This guide covers all deployment options with step-by-step instructions.

## 🌟 Quick Comparison

| Platform | Setup Time | Browser Conversion | Cost | Best For |
|----------|------------|-------------------|------|----------|
| **Cloudflare Pages** | 5 min | ✅ Works | Free | Best overall |
| **Netlify** | 5 min | ✅ Works | Free | Easy setup |
| **GitHub Pages** | 2 min | ⚠️ Fallback only | Free | Simplest |
| **Self-Host** | 30 min | ✅ Works | Varies | Full control |

## 📦 Option 1: Cloudflare Pages (Recommended)

**Why:** Free, fast CDN, supports required headers, browser conversion works perfectly.

### Steps:

1. **Create Cloudflare Account**
   - Go to [pages.cloudflare.com](https://pages.cloudflare.com/)
   - Sign up (it's free)

2. **Connect GitHub**
   - Click "Create a project"
   - Connect your GitHub account
   - Select your `cyd-video-converter` repository

3. **Configure Build**
   - Build command: (leave empty)
   - Build output directory: `/`
   - Click "Save and Deploy"

4. **Verify Headers**
   - The `_headers` file is automatically picked up
   - Wait for deployment to complete (~2 minutes)
   - Your site will be at: `https://your-project.pages.dev`

5. **Test**
   - Visit your site
   - Should see "✅ Browser Conversion Available!"
   - Try converting a small video

### Custom Domain (Optional):

1. In Cloudflare Pages dashboard, go to "Custom domains"
2. Add your domain (e.g., `convert.yourdomain.com`)
3. Follow DNS instructions
4. SSL is automatic!

---

## 📦 Option 2: Netlify

**Why:** Super easy, auto-deploys on git push, supports headers.

### Steps:

1. **Create Netlify Account**
   - Go to [netlify.com](https://www.netlify.com/)
   - Sign up with GitHub (easiest)

2. **New Site from Git**
   - Click "Add new site" → "Import an existing project"
   - Choose GitHub
   - Select `cyd-video-converter` repo

3. **Configure Build**
   - Build command: (leave empty)
   - Publish directory: `/`
   - Click "Deploy site"

4. **Verify**
   - Netlify automatically uses `netlify.toml` and `_headers`
   - Deployment takes ~1 minute
   - Your site: `https://random-name.netlify.app`

5. **Custom Domain (Optional)**
   - Click "Domain settings"
   - Add custom domain
   - Follow DNS instructions

### Auto-Deploy:
- Every push to `main` branch auto-deploys
- Pull requests get preview URLs
- Rollback with one click

---

## 📦 Option 3: GitHub Pages

**Why:** Simplest setup, but browser conversion won't work due to header limitations.

### Steps:

1. **Enable GitHub Pages**
   - Go to your repo → Settings → Pages
   - Source: "Deploy from a branch"
   - Branch: `main`, folder: `/ (root)`
   - Click "Save"

2. **Wait for Deployment**
   - GitHub Actions will build (check Actions tab)
   - Takes 2-5 minutes first time
   - Your site: `https://yourusername.github.io/cyd-video-converter/`

3. **What Works**
   - Page loads fine
   - Fallback options work perfectly
   - Users can download scripts
   - FFmpeg commands work

4. **What Doesn't Work**
   - In-browser conversion (no SharedArrayBuffer headers)
   - Site will show fallback options instead

### When to Use:
- You don't care about in-browser conversion
- Want simplest possible deployment
- Users are comfortable with scripts/FFmpeg

---

## 🔧 Option 4: Self-Hosting

**Why:** Full control, can host on your own domain/server.

### Requirements:
- Web server (Apache, Nginx, Caddy, etc.)
- Ability to set HTTP headers
- HTTPS (required for SharedArrayBuffer)

### Nginx Configuration:

```nginx
server {
    listen 443 ssl http2;
    server_name convert.yourdomain.com;

    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    root /var/www/cyd-converter;
    index index.html;

    # Required headers for SharedArrayBuffer
    add_header Cross-Origin-Embedder-Policy "require-corp" always;
    add_header Cross-Origin-Opener-Policy "same-origin" always;

    # Optional: Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

### Apache Configuration:

```apache
<VirtualHost *:443>
    ServerName convert.yourdomain.com
    DocumentRoot /var/www/cyd-converter

    SSLEngine on
    SSLCertificateFile /path/to/cert.pem
    SSLCertificateKeyFile /path/to/key.pem

    # Required headers for SharedArrayBuffer
    Header always set Cross-Origin-Embedder-Policy "require-corp"
    Header always set Cross-Origin-Opener-Policy "same-origin"

    # Optional: Security headers
    Header always set X-Frame-Options "SAMEORIGIN"
    Header always set X-Content-Type-Options "nosniff"

    <Directory /var/www/cyd-converter>
        Options -Indexes +FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>
</VirtualHost>
```

### Caddy Configuration (Easiest):

```caddy
convert.yourdomain.com {
    root * /var/www/cyd-converter
    file_server

    header {
        Cross-Origin-Embedder-Policy "require-corp"
        Cross-Origin-Opener-Policy "same-origin"
    }
}
```

### Deployment Steps:

1. **Upload Files**
   ```bash
   scp -r * user@yourserver:/var/www/cyd-converter/
   ```

2. **Configure Web Server**
   - Add configuration from above
   - Restart web server

3. **Get SSL Certificate**
   - Use Let's Encrypt (free): `certbot --nginx` or `certbot --apache`
   - Or use Caddy (automatic HTTPS)

4. **Test**
   - Visit your site
   - Check headers in browser DevTools → Network tab
   - Should see the COEP and COOP headers

---

## 🧪 Testing Your Deployment

### 1. Check Headers

Open browser DevTools (F12) → Network tab → Refresh page → Click on main document:

**Look for these response headers:**
```
cross-origin-embedder-policy: require-corp
cross-origin-opener-policy: same-origin
```

### 2. Test Browser Compatibility

Visit your deployed site. You should see one of:

**✅ Success:**
```
✅ Browser Conversion Available!
Your browser supports in-browser conversion.
```

**⚠️ Fallback:**
```
❌ In-Browser Conversion Not Supported
Your browser doesn't support SharedArrayBuffer.
```

### 3. Test Conversion

If browser conversion is available:
1. Select a preset
2. Drop a small test video (< 10MB)
3. Wait for conversion
4. Download should start automatically

---

## 🔧 Troubleshooting

### Headers Not Working

**Symptom:** Browser says SharedArrayBuffer not supported, but it should be.

**Fix:**
1. Check headers in DevTools Network tab
2. Make sure both COEP and COOP are present
3. Clear browser cache
4. Try incognito/private mode

### Cloudflare Pages Not Picking Up Headers

**Symptom:** Deployed but headers not applied.

**Fix:**
1. Make sure `_headers` file is in root directory
2. Redeploy the site
3. Wait 5 minutes for CDN to update
4. Check headers in DevTools

### Netlify Headers Not Working

**Symptom:** Deployed but SharedArrayBuffer unavailable.

**Fix:**
1. Check `netlify.toml` is in root
2. Syntax must be exact (check for typos)
3. Redeploy
4. Check Netlify deploy logs for errors

### Self-Host Headers Not Applied

**Symptom:** Server running but headers not in response.

**Fix:**
1. Check web server config syntax
2. Restart web server: `sudo systemctl restart nginx`
3. Check logs: `sudo tail -f /var/log/nginx/error.log`
4. Verify config loaded: `nginx -t`

---

## 📊 Monitoring & Analytics

### Cloudflare Pages:
- Built-in analytics dashboard
- Shows page views, bandwidth
- Free forever

### Netlify:
- Analytics available (paid)
- Deploy notifications
- Form handling (if you add a contact form)

### Self-Hosted:
- Use your existing analytics (Google Analytics, Plausible, etc.)
- Server logs for monitoring
- Uptime monitoring (UptimeRobot, etc.)

---

## 🔄 Updating

### Cloudflare/Netlify/GitHub Pages:

Just push to your repo:
```bash
git add .
git commit -m "Update converter"
git push origin main
```

Auto-deploys within 1-5 minutes.

### Self-Hosted:

```bash
# On your server
cd /var/www/cyd-converter
git pull origin main
```

No restart needed (static files).

---

## 💰 Cost Comparison

| Platform | Cost | Bandwidth | Notes |
|----------|------|-----------|-------|
| Cloudflare Pages | Free | Unlimited | Best value |
| Netlify | Free | 100GB/month | Plenty for most |
| GitHub Pages | Free | 100GB/month | Soft limit |
| Self-Host | ~$5-20/mo | Varies | VPS + domain |

**Recommendation:** Start with Cloudflare Pages (free + works perfectly). Upgrade to self-hosting only if you need custom features.

---

## 🎉 You're Done!

Your CYD Video Converter is now live! Share the URL with the community.

### Next Steps:
- Add to your CYD project documentation
- Share on ESP32 forums
- Star the GitHub repo
- Submit improvements via PR

Need help? [Open an issue](https://github.com/yourusername/cyd-video-converter/issues)!
