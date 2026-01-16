# Apify Social Media Scraper - Final Setup Guide

## Overview

This Make.com scenario uses Apify's pay-per-result actors to scrape posts from LinkedIn, Twitter/X, and Facebook profiles.

**Apify Actors Used:**
| Platform | Actor | Cost |
|----------|-------|------|
| LinkedIn | harvestapi/linkedin-profile-posts | ~$2 per 1,000 posts |
| Twitter | apidojo/tweet-scraper | ~$0.40 per 1,000 tweets |
| Facebook | apify/facebook-posts-scraper | Pay per usage |

---

## Step 1: Create Apify Account

1. Go to https://apify.com
2. Sign up for free account
3. You get $5 free credits to start

---

## Step 2: Get Your Apify API Token

1. Log into Apify Console
2. Click your profile icon (top right)
3. Go to **Settings** → **Integrations**
4. Copy your **Personal API Token**

It looks like: `apify_api_xxxxxxxxxxxxxxxxxxxx`

---

## Step 3: Google Sheet Setup

Your sheet should have these tabs:

### Tab 1: "Leads" (your input data)
| Lead ID | LinkedIn URL | Twitter URL | Facebook URL | Status |
|---------|--------------|-------------|--------------|--------|
| 1 | https://www.linkedin.com/in/username | https://x.com/username | https://www.facebook.com/pagename | |

### Tab 2: "Posts" (output - add these headers)
| Lead ID | Platform | Post ID | Post URL | Author | Content | Timestamp | Hashtags | Likes | Comments | Shares | Scraped At |

### Tab 3: "Errors" (error log - add these headers)
| Timestamp | Lead ID | Platform | URL | Status Code | Error Message |

---

## Step 4: Import Blueprint

1. Go to Make.com → Scenarios → Create New
2. Click ⋮ (More) → Import Blueprint
3. Upload `apify-social-scraper-final.json`

---

## Step 5: Configure the Scenario

### Module 1 (Get Leads):
- Connect Google account
- Select your spreadsheet
- Range: `Leads!A2:E100`

### Module 3 (Set Variables):
- Replace `__YOUR_APIFY_API_TOKEN__` with your actual Apify token

### All Google Sheets modules:
- Connect same Google account
- Select same spreadsheet
- Set correct sheet names (Posts, Errors, Leads)

---

## Step 6: Test It

1. Add one lead to your Leads sheet
2. Click "Run once" in Make.com
3. Check the Posts tab for results

---

## How It Works

1. Reads profile URLs from your Leads sheet
2. For each lead, calls Apify actors to scrape posts
3. Apify runs the scraper and returns posts directly
4. Posts are saved to your Posts sheet
5. Any errors go to the Errors sheet
6. Status column is updated

---

## Supported URL Formats

**LinkedIn:**
- `https://www.linkedin.com/in/username`
- `https://www.linkedin.com/company/companyname`

**Twitter/X:**
- `https://twitter.com/username`
- `https://x.com/username`

**Facebook:**
- `https://www.facebook.com/pagename`
- `https://www.facebook.com/username`

---

## Troubleshooting

**"Unauthorized" error:**
- Check your Apify API token is correct
- Make sure no extra spaces

**No posts returned:**
- Profile may be private
- Profile may have no recent posts
- Check Apify Console → Runs for details

**Timeout errors:**
- Apify actors can take 1-5 minutes
- The 300-second timeout should be enough
- If still timing out, run fewer leads at once

---

## Cost Estimate

For scraping 50 posts from each platform for 10 leads:
- LinkedIn: ~$1 (500 posts)
- Twitter: ~$0.02 (500 tweets)
- Facebook: ~$0.50-1

Total: ~$2-3 for 10 leads

---

*Created for Freelancer Project #40153500*
