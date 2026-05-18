# GitHub Pages Configuration for GeoSignal Dashboard

## 🎯 Overview

This file is specifically for **GitHub Pages deployment** and provides essential configuration information for deploying the GeoSignal dashboard using Streamlit on GitHub's hosting infrastructure.

---

## 🌐 Live URL

**Dashboard Location:** `https://alexnomads.github.io/geosignalagent/`

Wait ~2-3 minutes after pushing code for GitHub Pages to build and deploy!

---

## ⚙️ Configuration Files

### What This Directory Contains:

- **`.github/pages.json`** - Streamlit build engine configuration
- **`.github/workflows/deploy.yml`** - CI/CD deployment automation  
- **`.github/PAGES_README.md`** - This documentation file

---

## 📋 Enabling GitHub Pages (First Time)

### Step 1: Create `gh-pages` Branch

```bash
cd C:\Users\coliv\.openclaw\workspace\geosignalagent
git checkout -b gh-pages
# Or if branch exists:
git checkout gh-pages
```

### Step 2: Set Up GitHub Pages in Settings

Visit: `https://github.com/alexnomads/geosignalagent/settings/pages`

- **Source:** Select `gh-pages` branch (or configure appropriately)
- **Build and deploy on commit:** ✅ Enabled
- Click "Save"

### Step 3: Push to gh-pages Branch

```bash
git push -u origin gh-pages
```

This creates the public deployment target for GitHub Pages!

---

## 🔄 Deployment Workflow

The workflow in `.github/workflows/deploy.yml` handles automatic deployment.

### What Happens on Each Push:

1. **Trigger:** Push to `main` branch OR cron schedule (daily at 11pm UTC)
2. **Run on Ubuntu runner** - Private GitHub Actions server
3. **Install dependencies** - Python 3.11 + Streamlit + requests
4. **Fetch data with API keys** - Server-side using secrets from repo
5. **Generate JSON files** - signals.json, markets.json, whales.json, articles.json
6. **Commit to gh-pages** - Update only `data/` directory
7. **GitHub Pages rebuilds** - Static site generated from Streamlit app
8. **Live URL updated** - New content available in 2-3 minutes

---

## 🔐 API Keys & Secrets

### How to Set Up Secrets:

1. Go to `Settings → Secrets and variables → Actions`
2. Click "New repository secret"
3. Add these secrets:

| Secret Name | Description | Example Value |
|------------|-------------|---------------|
| `POLYMARKET_API_KEY` | Fetch market data from Polymarket API | `pk_live_your_api_key_here` |
| `ALCHEMY_POLYGON_API_KEY` | (Optional) Fetch whale data via Alchemy RPC | `sk_live_your_alchemy_key` |

### Security Notes:

- ✅ Secrets are encrypted at rest on GitHub
- ✅ Never visible in repository code or logs
- ✅ Only available to workflow runs with appropriate permissions
- ⚠️ Rotate keys every 90 days for security best practice

---

## 📊 Workflow Configuration

### Current Schedule (Default):

```yaml
schedule:
  # Daily at 11pm UTC (7pm Madrid time)
  - cron: '0 23 * * *'
```

### Change Schedule:

Edit `.github/workflows/deploy.yml` to change frequency:

```yaml
# Every hour:
- cron: '0 * * * *'

# Every 6 hours:  
- cron: '0 */6 * * *'

# Disable automatic scheduling (push-trigger only):
schedule: []
```

### Manual Trigger:

Click "Run workflow" button in Actions tab → Select `Geosignal Dashboard Deploy` → Run

---

## 🎨 GitHub Pages Settings Configuration

### Required Settings:

**Repository:** Make public for standard GitHub Pages hosting

**Source Branch:**
- **Recommended:** `gh-pages` branch
- **Alternative:** `main` branch (requires additional domain configuration)

**Build and deploy on commit:** ✅ Enabled

**Custom Domain:** (Optional) If using custom domain like `geo.nomads.github.io`, configure in Settings → Pages → Custom domain

---

## 📁 File Structure on GitHub Pages

### What Gets Deployed:

```
/
├── index.html                    # Main dashboard entry point
├── static/
│   ├── css/                      # CSS files
│   ├── js/                       # JavaScript files
│   └── images/                   # Static assets
├── data/
│   ├── signals.json              # Pre-fetched trading signals
│   ├── markets.json               # Market data (live prices, volumes)
│   ├── whales.json                # Whale wallet activity
│   └── articles.json             # News aggregation from RSS feeds
├── .streamlit/
│   └── config.toml               # Streamlit configuration
├── pages-metadata.json           # Build metadata for Pages
└── favicon.ico                   # Browser tab icon (if provided)
```

### Generated Files:

GitHub Actions generates these automatically via `.github/workflows/deploy.yml`:

- `index.html` - Main HTML entry point for Streamlit app
- `data/*.json` - Pre-fetched data files
- `pages-metadata.json` - Build information
- Various cached files optimized for CDN delivery

---

## 🚀 Performance & Caching

### GitHub Pages Features:

- **Free SSL Certificate** - All connections encrypted via HTTPS
- **Global CDN** - Served from edge locations worldwide
- **Automatic Compression** - Content served with gzip/br compression
- **HTTP Caching** - Browser and CDN caching enabled for static assets
- **Fast Build Times** - Streamlit engine optimized for GitHub Pages

### Best Practices:

1. Minimize data file sizes (current implementation handles well)
2. Use relative paths in frontend code
3. Leverage browser caching for static assets
4. Update `.streamlit/config.toml` if custom settings needed

---

## 🧪 Testing & Verification

### Before Going Live:

1. **Local Testing:**
   ```bash
   streamlit run app.py --server.headless true --server.port 8501
   ```
   Visit `http://localhost:8501` to verify locally.

2. **GitHub Pages Preview:**
   - Push code to trigger workflow
   - Wait ~3 minutes for build
   - Click "Pages" badge or visit live URL
   - Verify all 4 views functional

3. **Browser DevTools:**
   - Open DevTools (F12)
   - Check Console tab for any errors
   - Network tab to verify API calls not from frontend
   - Inspect data files being loaded

### Success Indicators:

- ✅ Dashboard loads without console errors
- ✅ All views accessible via sidebar navigation
- ✅ Real-time stats showing in sidebar
- ✅ Data displays correctly on each view
- ✅ No loading spinner stuck indefinitely

---

## 🔧 Troubleshooting Common Issues

### Issue: "Page not found" or 404

**Solutions:**
1. Wait up to 10 minutes for build completion
2. Check workflow status in Actions tab
3. Verify `gh-pages` branch exists and has commits
4. Ensure `.github/pages.json` file is present

### Issue: Build fails with "No API key found"

**Solutions:**
1. Verify secrets are set in repo settings (`Settings → Secrets`)
2. Check secret names are exact (case-sensitive)
3. Confirm keys have valid prefix (`pk_live_`, `sk_live_`)
4. Look for error logs in workflow run details

### Issue: Data not appearing on dashboard

**Solutions:**
1. Workflow may not have run yet - wait 2-3 minutes
2. Check workflow completed successfully (green checkmark)
3. Verify data files exist in `data/` directory after build
4. Look for error messages in workflow logs

### Issue: Slow page load times

**Solutions:**
1. Optimize JSON file sizes if possible
2. Consider pagination for very long lists
3. Add loading spinners during data fetch (not applicable here as we preload)
4. Check CDN performance (usually excellent with GitHub Pages)

---

## 📈 Monitoring & Maintenance

### Daily Checks:

- Monitor Actions tab for failed workflow runs
- Check for API rate limit warnings in logs
- Verify data freshness timestamps in JSON files

### Weekly Reviews:

- Review dashboard analytics (if integrated)
- Check for breaking changes in upstream APIs
- Update documentation if features added

### Monthly Maintenance:

- Rotate GitHub secrets for security
- Clean up workflow artifacts if needed
- Review and update cron schedule frequency

---

## 🔐 Security Checklist

Before deploying to production:

- [x] API keys stored in GitHub repo secrets only
- [x] No sensitive data in repository code
- [x] `.gitignore` excludes local env files
- [x] Workflow uses latest dependency versions
- [x] Repository permissions reviewed regularly
- [x] Access logs monitored for suspicious activity

---

## 🆘 Getting Help

### Common Resources:

1. **GitHub Pages Documentation:** https://pages.github.com
2. **Streamlit Deployment Docs:** https://docs.streamlit.io/streamlit-cloud/overview
3. **Workflow Debugging Guide:** https://docs.github.com/actions/managing-workflow-runs/debugging-a-workflow-run

### Support Channels:

- Check workflow logs in Actions tab for detailed error messages
- Review `.github/PAGES_README.md` for configuration guidance  
- Contact repository maintainers if issues persist

---

## 📝 Notes

### GitHub Pages Limits (Free Tier):

- **Storage:** Unlimited (10GB recommended)
- **Bandwidth:** 200 GB/month on primary custom domain, unlimited with github.io domains
- **Build Time:** No strict limit but large builds may take longer
- **Cron Jobs:** Maximum 4 concurrent runs for free tier (not an issue here)

### Rate Limiting:

- GitHub Actions free tier: ~2,000 minutes/month compute time
- Polymarket API: Generous free tier, monitor usage in dashboard logs
- Alchemy RPC: 5M free polygon scan units/month

---

**Last Updated:** May 18, 2026  
**Version:** 2.0.0  
**Maintainer:** GeoSignal Development Team
