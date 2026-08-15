# NextDNS Denylist Auto-Updater

Automatically sync your NextDNS denylists from one or more upstream blocklists using GitHub Actions. Runs daily, removes stale domains (those no longer in the source), and imports new ones — keeping your NextDNS profiles perfectly in sync without manual intervention.

---

## Why This Exists

NextDNS offers curated blocklists under their Privacy tab, but doesn't support adding custom list URLs directly, you're limited to whatever they've vetted and chosen to include. Community contributions to their [blocklists repository](https://github.com/nextdns/blocklists) are met with silence. Case in point: [PR #170](https://github.com/nextdns/blocklists/pull/170), submitted in December 2024 to add the ph00lt0 blocklist, actively maintained, with broad coverage across countries and categories missing from NextDNS's existing offerings, has sat untouched with zero review or response. 

So instead of waiting for NextDNS to acknowledge community lists, this workflow takes matters into its own hands: download, sync, and prune denylists automatically via the API. If they won't host your desired list, you can still use it.

---

## Features

- **Multi-account support** — manage multiple NextDNS accounts via API keys
- **Multi-blocklist support** — combine any number of blocklist URLs into one unified denylist
- **Stale domain cleanup** — automatically remove domains deleted from upstream sources
- **Delta-aware operations** — only makes API calls that are actually needed, respecting NextDNS rate limits
- **Zero local state** — all state lives in NextDNS, nothing to commit beyond your config

---

## Setup

### 1. Fork the Repository

1. Click **Fork** at the top right of this repository
2. Click **Create fork**

You now have your own copy at `github.com/<your-username>/custom-blocklists-nextdns-updater`.

### 2. Add Your NextDNS API Keys as Secrets

The included `blocklists.txt` ships with the [ph00lt0 blocklist](https://github.com/ph00lt0/blocklist) by default. You can add more later.

First, get your API key:
1. Log in to [my.nextdns.io/account](https://my.nextdns.io/account)
2. Scroll to the **API** section
3. Copy your API key

Then add it to GitHub:
1. Go to your forked repo on GitHub
2. Click **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**

Add one secret per NextDNS account using this naming convention:

| Secret Name                   | Value              |
|-------------------------------|--------------------|
| `NEXTDNS_ACCOUNT_1_API_KEY`  | Your first API key |
| `NEXTDNS_ACCOUNT_2_API_KEY`  | Your second API key|
| `NEXTDNS_ACCOUNT_3_API_KEY`  | Your third API key |

Continue incrementing the number for each additional account. The script auto-detects how many accounts exist by checking for sequential numbered secrets — it stops at the first gap.

### 3. Run the Workflow

The workflow runs automatically every day at 02:00 UTC. To trigger it manually right away:

1. Go to the **Actions** tab in your fork
2. Select **Update NextDNS Denylist** from the left sidebar
3. Click **Run workflow** → select your branch → **Run workflow**
4. Click the run to view live logs

---

## Adding More Blocklists (Optional)

The included `blocklists.txt` contains ph00lt0 Blocklist. You can add others as like to the txt file.
All sources are merged and deduplicated automatically. If you remove a URL from this file, all domains unique to that source will be cleaned up on the next run.


## First Run Warning

> [!WARNING]
> On the first run after enabling stale cleanup, any domains already in your NextDNS denylist that are not in the blocklists will be flagged as stale and removed. If you have manually added domains to NextDNS that you want to keep, either add their source to `blocklists.txt` or add them to a custom list hosted somewhere accessible by URL.

## Limitations
Extremely large blocklists may hit NextDNS API rate limits. nextdnsctl handles retries, but monitor logs for warnings.
The script assumes accounts are numbered sequentially (1, 2, 3...). If you skip a number, all accounts after the gap are ignored.
GitHub Actions has a maximum runtime of 6 hours per job. Very large multi-account setups should monitor execution time.


## Attribution

- nextdnsctl — Community CLI for NextDNS management (MIT licensed)
- ph00lt0/blocklist — Default included blocklist source


## License
MIT License — see LICENSE file for details.
