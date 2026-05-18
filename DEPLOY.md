# GeoSignal Dashboard - Production Deployment Guide

## ✅ Overview

This repository contains a **production-ready, minimal GeoSignal dashboard** designed specifically for secure deployment on GitHub Pages using server-side data fetching.

---

## 🎯 What You're Deploying

- **Streamlit Frontend**: Modern, responsive dashboard
- **Server-Side Data Fetching**: Secure API integration via GitHub Actions
- **Static Site Generation**: No API keys exposed in HTML/CSS/JS
- **GitHub Pages Hosting**: Free CDN hosting at `https://alexnomads.github.io/geosignalagent/`

---

## 🔐 Security Architecture

### How It Works:

```
┌─────────────────────────┐
│ GITHUB REPO SECRETS     │  ← Encrypted keys stored here
│ (Settings → Secrets)    │
└─────────────────────────┘
          ↓
┌─────────────────────────┐
│ GitHub Actions Runner   │  ← Private server-side fetching
│ Uses API keys privately  │
└─────────────────────────┘
          ↓
┌─────────────────────────┐
│ Pre-fetch data to JSON  │  ← No secrets in code
└─────────────────────────┘
          ↓
┌─────────────────────────┐
│ Push to gh-pages branch │  ← Only static files
└─────────────────────────┘
          ↓
┌─────────────────────────┐
│ GITHUB PAGES CDN        │  ← Public frontend only
│ Serves HTML/CSS/JS      │
└─────────────────────────┘
```

**Security Guarantees:**
- ✅ API keys never touch public HTML/CSS/JS files
- ✅ All API calls happen on private GitHub Actions runners
- ✅ Only static, pre-fetched data served to users
- ✅ No client-side authentication or exposed secrets

---

## 🚀 Initial Deployment (First Time)

### Step 1: Set Up GitHub Repository

```bash
# Clone existing repo or create new one
cd C:\Users\coliv\.openclaw\workspace\geosignalagent
git init  # if not already initialized

# Create production branch
git checkout -b gh-pages || git branch --set-upstream-to=origin/gh-pages
```

### Step 2: Configure GitHub Secrets

Go to your repository → `Settings` → `Secrets and variables` → `Actions`

Click "New repository secret" and add:

| Secret Name | Value Format | Required | Description |
|------------|--------------|----------|-------------|
| `POLYMARKET_API_KEY` | `pk_live_...` | ✅ Yes | Polymarket API key for market data fetching |
| `ALCHEMY_POLYGON_API_KEY` | `sk_live_...` | ⚠️ Optional | Alchemy RPC key for whale tracking (Polygon network) |

**Important:** These keys are **never visible** in the repository code or history. They're encrypted at rest!

---

### Step 3: Enable GitHub Pages

Go to: `https://github.com/alexnomads/geosignalagent/settings/pages`

- **Source:** Select `gh-pages` branch (or configure if needed)
- **Build and deploy on commit:** ✅ Enabled  
- Click "Save"

---

### Step 4: Push Initial Code

```bash
cd C:\Users\coliv\.openclaw\workspace\geosignalagent

# Add all files to git
git add .

# Commit with message indicating production deployment
git commit -m "chore: Initial production deployment - minimal GeoSignal dashboard"

# Push to main branch (triggers deploy.yml workflow)
git push origin main
```

---

### Step 5: Verify Deployment

1. **Check GitHub Actions:**
   - Go to `Settings → Actions`
   - You should see one "Geosignal Dashboard Deploy" workflow run
   - Wait ~3-5 minutes for completion

2. **View Build Logs:**
   - Click on the workflow run
   - Check each step completed successfully
   - Look for any error messages

3. **Visit Live URL:**
   - Wait 2-3 minutes after workflow completes
   - Go to: `https://alexnomads.github.io/geosignalagent/`
   - Dashboard should load with demo/pre-loaded data

4. **Success Indicators:**
   - ✅ No JavaScript console errors
   - ✅ All 4 views accessible (Signals, Articles, Markets, Whales)
   - ✅ Real-time stats showing in sidebar

---

## 🔄 Subsequent Updates

### Normal Workflow:

Every time you push to `main` branch:

1. GitHub Actions automatically triggers `deploy.yml`
2. Fetches latest data using private API keys
3. Pushes updated files to `gh-pages` branch
4. GitHub Pages rebuilds automatically (~2 minutes)
5. Live URL updates with new data

### Manual Refresh:

To force immediate data refresh:

1. Go to `Settings → Actions → Geosignal Dashboard Deploy`
2. Click "Run workflow" button
3. Wait ~5 minutes for build
4. Visit live URL again

---

## 📊 Cron Schedule Configuration

By default, data fetches **daily at 11pm UTC**.

### To Change Schedule:

Edit `.github/workflows/deploy.yml`:

```yaml
schedule:
  # Current: Daily at 11pm UTC (7pm Madrid time)
  - cron: '0 23 * * *'
  
  # Examples of other schedules:
  # Hourly:    - cron: '0 * * * *'
  # Every 6 hours: - cron: '0 */6 * * *'
  # Every 12 hours: - cron: '0,12 * * * *'
  
  # Or disable automatic scheduling (push-trigger only):
  schedule: []
```

---

## 🧪 Testing Before Deployment

### Test Locally First:

If you want to verify locally before pushing to production:

```bash
cd C:\Users\coliv\.openclaw\workspace\geosignalagent

# Create demo mode (no API keys)
export DEMO_MODE=true
export POLYMARKET_API_KEY=""  # Empty key triggers demo data

# Run data fetcher
python scripts/fetch_all_data.py

# Test locally
streamlit run app.py --server.headless true --server.port 8501
```

Visit: `http://localhost:8501` to see dashboard locally.

---

## 🛠️ Troubleshooting

### Problem: "No API key found" errors in workflow logs

**Solution:**
1. Check GitHub Secrets are set in repo settings
2. Secret names must be exact (case-sensitive): `POLYMARKET_API_KEY`
3. Keys should start with prefix (e.g., `pk_live_` for Polymarket)

### Problem: Dashboard shows empty/no data

**Solution:**
1. Workflow may not have run yet - wait 2-3 minutes
2. Check workflow status in `Settings → Actions`
3. Look for data files being created in GitHub UI after build
4. If still empty, trigger manual workflow run

### Problem: Build fails with "Streamlit installation failed"

**Solution:**
1. Check Python version compatibility (requires Python 3.8+)
2. Verify `requirements.txt` if needed (currently not required)
3. Look at build logs for specific error message
4. Try rebuilding workflow manually

### Problem: GitHub Pages not loading at all

**Solution:**
1. Verify `.github/pages.json` file exists in repo root
2. Check GitHub Pages settings point to `gh-pages` branch
3. Confirm repository is public (private repos need special setup)
4. Wait up to 10 minutes for first build completion

---

## 📈 Monitoring & Maintenance

### Check Deployment Status:

```bash
# View recent workflow runs
gh run list --repo alexnomads/geosignalagent --limit 5
```

Or via GitHub UI: `Settings → Actions`

### Monitor API Usage:

**Polymarket API Limits:**
- Free tier allows generous usage (check [Polymarket docs](https://polidrome.com))
- Typical dashboard usage: ~10-50 API calls/day
- No rate limiting for demo mode

**Alchemy RPC Limits:**
- Free tier: 5M polygon scan units/month
- Typical whale tracking: ~500-2000 units/month
- Sufficient for production use

### Update Frequency Recommendations:

| Data Type | Update Frequency | Rationale |
|-----------|------------------|-----------|
| Trading Signals | Daily (11pm UTC) | Market odds change hourly, daily sufficient |
| Whale Activity | Hourly (optional) | High-value data but less frequent updates ok |
| News Articles | Every 30-60 min | Breaking news needs fresh content |
| Market Volumes | Real-time via API poll | Polymarket API supports real-time polling |

---

## 🔒 Security Best Practices

### Before Going Live:

1. ✅ Generate strong, secure API keys (not using test/demo keys)
2. ✅ Rotate keys every 90 days or after suspicious activity
3. ✅ Use different credentials for demo vs production
4. ✅ Store actual keys in GitHub secrets, not code
5. ✅ Review GitHub Actions logs weekly for errors

### What NOT to Do:

- ❌ Never commit API keys to repository
- ❌ Never use `.env` files with actual secrets (use workflow env vars)
- ❌ Never deploy with test/demo API keys in production
- ❌ Never share GitHub secret URLs publicly

### Monitoring:

Set up alerts for:
- Failed workflow runs
- API rate limit warnings
- Unusual data pattern changes
- Build time degradation

---

## 🆘 Getting Help

### Common Issues Reference:

**Q: Dashboard shows "Loading..." forever?**  
A: GitHub Pages build taking longer than expected. Wait up to 10 minutes. Check workflow status in `Settings → Actions`.

**Q: Can't access the dashboard at all?**  
A: Verify GitHub Pages is enabled and correct branch (`gh-pages`) is selected in settings. Repository must be public (or use private domain).

**Q: "404 Not Found" on live URL?**  
A: Workflow hasn't completed yet. Refresh after 2-3 minutes. Or manually trigger workflow run from `Settings → Actions`.

**Q: Data not updating automatically?**  
A: Cron schedule is disabled by default in some configs. Check workflow for schedule entry. Enable if needed.

---

## 📚 Additional Resources

- [Streamlit Docs](https://docs.streamlit.io)
- [GitHub Pages Documentation](https://pages.github.com)
- [Polymarket API Documentation](https://polidrome.com/)
- [Alchemy RPC Documentation](https://www.alchemy.com/)

---

## 🎯 Final Checklist

Before marking deployment as complete:

- [ ] GitHub Secrets configured (POLYMARKET_API_KEY set)
- [ ] GitHub Pages enabled on `gh-pages` branch
- [ ] First workflow run completed successfully
- [ ] Dashboard loads without errors at live URL
- [ ] All 4 views functional (Signals, Articles, Markets, Whales)
- [ ] Sidebar stats showing real-time data
- [ ] No console errors in browser DevTools
- [ ] Cron schedule set to desired frequency

---

**Congratulations! Your GeoSignal dashboard is now securely deployed and live!** 🎉

Visit: `https://alexnomads.github.io/geosignalagent/`
