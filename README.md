# GeoSignal Dashboard - Production Deployment

🌍 **Live at:** `https://alexnomads.github.io/geosignalagent/`

---

## 🎯 What is GeoSignal?

A production-ready geopolitical trading signals dashboard that:

- 📰 Monitors 6+ geopolitical RSS feeds in real-time
- 💹 Fetches live Polymarket market prices and volumes  
- 🐋 Tracks whale wallet activity via Alchemy RPC
- 📊 Calculates trading signals with edge quantification & Kelly sizing
- 🔐 Uses **server-side data fetching** - API keys NEVER exposed publicly

---

## 🌐 Access the Dashboard

**Public URL:** `https://alexnomads.github.io/geosignalagent/`

Wait ~2 minutes after pushing code for GitHub Pages to build and deploy!

---

## 🎨 Dashboard Features

### 🎯 Signals View
- Trading opportunities sorted by edge & confidence
- CRITICAL/HIGH/MEDIUM/Low confidence badges
- Real-time price data from Polymarket API
- Kelly criterion position sizing ready

### 📰 Articles View  
- Live geopolitical news from 6 major sources:
  - Reuters World News
  - BBC World Service
  - AP News International
  - New York Times World
  - The Guardian
  - Wall Street Journal

### 📈 Markets View
- Top Polymarket markets sorted by volume
- Sortable by Volume, YES%, or End Date
- Full market table with prices and URLs

### 🐋 Whales View
- Tracked whale wallets (21+ elite traders)
- On-chain activity monitoring via Alchemy RPC
- USDC balance tracking
- Transaction count analysis

---

## 🔐 Security Architecture

### How This Stays Secure:

```
GitHub Repo Secrets (Private, Encrypted)
    ↓
GitHub Actions Runner (Server-Side Fetching)
    ↓  Uses API keys privately
data/*.json (Pre-fetched Data)
    ↓
GitHub Pages CDN (Public Frontend)
    → Only serves static HTML/CSS/JS files
```

**Key Points:**
- ✅ API keys stored in GitHub repo secrets only
- ✅ Data fetched via CI/CD pipeline with encrypted keys
- ✅ No direct API calls from public frontend HTML
- ✅ Pre-fetched JSON files serve dashboard data safely

---

## 🚀 Deployment Checklist

### Before First Deploy:

1. **Create GitHub Repository**
   - New repo: `geosignalagent` or clone existing
   - Make repository **public** for GitHub Pages deployment

2. **Set Up GitHub Secrets**
   Go to: `Settings → Secrets and variables → Actions`

   Add these secrets:

   | Secret Name | Example Value | Required |
   |------------|---------------|----------|
   | `POLYMARKET_API_KEY` | `pk_live_...` | ✅ Yes |
   | `ALCHEMY_POLYGON_API_KEY` | `sk_live_...` | ⚠️ Optional |

3. **Enable GitHub Pages**
   Go to: `Settings → Pages`

   - Source: Select `gh-pages` branch
   - Build and deploy on commit: ✅ Enabled
   - Click "Save"

4. **Push Initial Code**

   ```bash
   cd C:\Users\coliv\.openclaw\workspace\geosignalagent

   git add .
   git commit -m "chore: Initial production deployment to GitHub Pages"
   git push origin main  # Will trigger deploy.yml workflow
   ```

5. **Verify Deployment**
   - Wait ~2-3 minutes for build
   - Check logs at: `Settings → Actions → Geosignal Dashboard Deploy`
   - Visit: `https://alexnomads.github.io/geosignalagent/`

---

## 🔄 How It Works

### Automated Workflow:

1. **Push to `main` branch** (or cron schedule triggers)
2. **GitHub Actions runs** `.github/workflows/deploy.yml`
3. **Fetches latest data** from Polymarket API using private key
4. **Saves JSON files** to `data/` directory
5. **Pushes to `gh-pages` branch** via CI/CD
6. **GitHub Pages serves** static frontend automatically

### Cron Schedule:

Data refreshes **daily at 11pm UTC** (7pm Madrid time) by default.  
To change schedule, edit `.github/workflows/deploy.yml`.

---

## 📊 Live Data Sources

- **Polymarket API** - Market prices and volumes
- **Alchemy RPC** - Whale wallet activity monitoring
- **RSS Feeds** - Geopolitical news aggregation:
  - Reuters World News (5 min updates)
  - BBC World Service
  - AP News International
  - New York Times World
  - The Guardian
  - Wall Street Journal

---

## 🔧 Manual Refresh

To force a data refresh immediately:

1. Go to `Settings → Actions → Geosignal Dashboard Deploy`
2. Click "Run workflow" button
3. Wait ~5 minutes for build

Or via CLI:

```bash
git push origin main  # Triggers workflow automatically
```

---

## 🛠️ Troubleshooting

### Data not updating?
- Check GitHub Actions logs in `Settings → Actions`
- Verify secrets are set correctly in repo settings
- Run workflow manually to debug

### Dashboard showing errors?
- Make sure `app.py` is at root (not in subfolder)
- Check GitHub Pages settings: select `gh-pages` branch
- Wait ~2 minutes for build to complete
- Look for `.pages-metadata.json` being created

### GitHub Pages not building?
- Go to `Settings → Pages`
- Verify source branch is set to `gh-pages`
- Check for `.github/pages.json` file exists
- Ensure Streamlit engine is selected

---

## 📁 File Structure

```
geosignalagent/
├── app.py                          # Main dashboard frontend
├── README.md                       # This file
├── DEPLOY.md                       # Deployment guide
├── .github/
│   ├── pages.json                  # Pages build config
│   ├── PAGES_README.md             # GitHub Pages docs
│   └── workflows/
│       └── deploy.yml              # CI/CD deployment automation
└── data/
    ├── .gitkeep                    # Directory placeholder
    ├── signals.json                # Auto-generated by workflow
    ├── markets.json                # Auto-generated by workflow
    ├── whales.json                 # Auto-generated by workflow
    └── articles.json               # Auto-generated by workflow
```

**Note:** `data/*.json` files are auto-generated by GitHub Actions workflow on first deploy!

---

## 🔒 Security Best Practices

- ✅ Never commit API keys to repository
- ✅ Use GitHub repo secrets for all sensitive data
- ✅ Keep `.env` files in `.gitignore` or use workflow env vars
- ✅ Review deployment logs regularly in `Settings → Actions`
- ✅ Monitor GitHub Pages build logs for errors

---

## 📞 Support

If you encounter issues:

1. Check GitHub Actions workflow logs
2. Verify secrets are configured in repo settings
3. Review `.github/PAGES_README.md` for deployment-specific guidance
4. Visit `https://alexnomads.github.io/geosignalagent/` after build completes

---

**© 2026 GeoSignal** - All rights reserved | [Privacy Policy](#)
