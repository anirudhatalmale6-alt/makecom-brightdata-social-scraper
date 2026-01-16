# Bright Data Social Media Scraper - Make.com Blueprint

## Complete Setup & Documentation Guide

---

## Table of Contents

1. [Overview](#overview)
2. [Prerequisites](#prerequisites)
3. [Google Sheet Setup](#google-sheet-setup)
4. [Bright Data Account Setup](#bright-data-account-setup)
5. [Make.com Blueprint Import](#makecom-blueprint-import)
6. [Configuration Steps](#configuration-steps)
7. [Module-by-Module Explanation](#module-by-module-explanation)
8. [How to Trigger the Scenario](#how-to-trigger-the-scenario)
9. [Scheduling](#scheduling)
10. [Troubleshooting](#troubleshooting)

---

## Overview

This Make.com scenario scrapes social media posts from **Facebook**, **Twitter (X)**, and **LinkedIn** using the Bright Data Web Scraper API. It automatically:

- Scrapes posts from the **last 7 days** (calculated dynamically)
- Saves all posts to a **Google Sheet** (Posts tab)
- Logs any errors to a separate **Errors tab**
- Returns a **JSON response** via webhook for integration with other tools

### Data Fields Collected

| Field | Description |
|-------|-------------|
| Platform | Facebook, Twitter, or LinkedIn |
| Post ID | Unique identifier for the post |
| URL | Direct link to the post |
| Author | Page name, username, or profile URL |
| Content | Full text of the post |
| Timestamp | When the post was published |
| Hashtags | Comma-separated list of hashtags |
| Likes | Number of likes/reactions |
| Comments | Number of comments/replies |
| Shares/Reposts | Number of shares (where available) |
| Scraped At | When this data was collected |

---

## Prerequisites

Before starting, ensure you have:

1. **Make.com account** (free tier works for testing)
2. **Bright Data account** with API access (free trial available)
3. **Google account** for Google Sheets integration

---

## Google Sheet Setup

### Step 1: Create a New Google Sheet

1. Go to [Google Sheets](https://sheets.google.com)
2. Create a new spreadsheet
3. Name it: `Social Media Scraper Data`

### Step 2: Create the "Posts" Sheet

1. Rename the first sheet tab to: `Posts`
2. Add these headers in Row 1:

```
A1: Platform
B1: Post ID
C1: URL
D1: Author
E1: Content
F1: Timestamp
G1: Hashtags
H1: Likes
I1: Comments
J1: Shares
K1: Scraped At
```

### Step 3: Create the "Errors" Sheet

1. Click the **+** button to add a new sheet tab
2. Name it: `Errors`
3. Add these headers in Row 1:

```
A1: Timestamp
B1: Platform
C1: URL Attempted
D1: Status Code
E1: Error Message
F1: Snapshot ID
```

### Step 4: Note Your Spreadsheet ID

1. Look at your Google Sheet URL:
   ```
   https://docs.google.com/spreadsheets/d/SPREADSHEET_ID_HERE/edit
   ```
2. Copy the `SPREADSHEET_ID_HERE` part - you'll need this later

---

## Bright Data Account Setup

### Step 1: Create a Free Account

1. Go to [Bright Data](https://brightdata.com)
2. Sign up for a free account (includes trial credits)

### Step 2: Get Your API Token

1. Log into your Bright Data dashboard
2. Click on your **profile icon** (top right)
3. Go to **Account Settings** > **API tokens**
4. Click **Create new token**
5. Name it: `Make.com Social Scraper`
6. Copy the token and save it securely

**Important:** The token looks like: `a1b2c3d4-e5f6-7890-abcd-ef1234567890`

### Step 3: Verify Dataset Access

The scenario uses these Bright Data datasets:

| Platform | Dataset ID | Dataset Name |
|----------|-----------|--------------|
| Facebook | `gd_lkaxegm826bjpoo9m5` | Facebook Posts |
| Twitter | `gd_lwxmeb2u1cniijd7t4` | Twitter Posts by Profile |
| LinkedIn | `gd_lyy3tktm25m4avu764` | LinkedIn Posts |

These are standard Web Scraper API datasets included with your account.

---

## Make.com Blueprint Import

### Step 1: Download the Blueprint

Save the `brightdata-social-scraper-v2.json` file to your computer.

### Step 2: Import into Make.com

1. Log into [Make.com](https://www.make.com)
2. Go to **Scenarios** in the left menu
3. Click **Create a new scenario**
4. Click the **three dots** (more options) in the bottom toolbar
5. Select **Import Blueprint**
6. Upload the JSON file
7. Click **Save**

---

## Configuration Steps

After importing, you need to configure 4 things:

### 1. Configure the Webhook (Module 1)

1. Click on the first module (Webhook)
2. Click **Add** to create a new webhook
3. Name it: `Social Scraper Trigger`
4. Click **Save**
5. **Copy the webhook URL** - you'll need this to trigger the scenario

The webhook URL looks like:
```
https://hook.us1.make.com/abc123xyz789...
```

### 2. Add Your Bright Data API Key (Module 2)

1. Click on Module 2 (Set Date Variables)
2. Find the variable named `brightdata_api_key`
3. Replace `__YOUR_BRIGHTDATA_API_KEY__` with your actual API token:
   ```
   a1b2c3d4-e5f6-7890-abcd-ef1234567890
   ```
4. Click **OK**

### 3. Connect Google Sheets

For each Google Sheets module (there are 6 total - 3 for posts, 3 for errors):

1. Click on the module
2. Click **Add** next to Connection
3. Select **Google Sheets (OAuth)**
4. Sign in with your Google account
5. Grant permissions

### 4. Configure Spreadsheet IDs

For each Google Sheets module:

1. Click on the module
2. For **Spreadsheet ID**: Enter your spreadsheet ID from earlier
3. For **Sheet ID**:
   - Posts modules: Select `Posts` (or enter the sheet name)
   - Errors modules: Select `Errors` (or enter the sheet name)

**Tip:** After connecting Google Sheets, Make.com can auto-detect your sheets. Just select them from the dropdown.

---

## Module-by-Module Explanation

### Module 1: Webhook Trigger
**Type:** `gateway:CustomWebHook`
**Purpose:** Entry point for the scenario. Receives HTTP requests with URLs to scrape.

**Input Parameters:**
- `facebook_urls`: Array of Facebook page/profile URLs
- `twitter_urls`: Array of Twitter profile URLs
- `linkedin_urls`: Array of LinkedIn profile/post URLs

---

### Module 2: Set Date Variables
**Type:** `util:SetVariables`
**Purpose:** Calculates the date range for scraping (last 7 days).

**Variables Set:**
- `end_date`: Today's date (MM-DD-YYYY format)
- `start_date`: 7 days ago (MM-DD-YYYY format)
- `brightdata_api_key`: Your API token

---

### Module 10: Router - Platform Split
**Type:** `builtin:BasicRouter`
**Purpose:** Splits the flow into 3 parallel routes (Facebook, Twitter, LinkedIn).

Each route only executes if URLs are provided for that platform.

---

### Modules 101-108: Facebook Route

| ID | Module | Purpose |
|----|--------|---------|
| 101 | Iterator | Loops through each Facebook URL |
| 102 | HTTP Request | Triggers Bright Data Facebook scraper API |
| 103 | Sleep | Waits 45 seconds for scraping to complete |
| 104 | HTTP Request | Retrieves the scraped data (snapshot) |
| 105 | Router | Checks if data is ready (status 200) or error |
| 106 | Iterator | Loops through each post in the results |
| 107 | Google Sheets | Adds successful posts to the Posts sheet |
| 108 | Google Sheets | Logs errors to the Errors sheet |

---

### Modules 201-208: Twitter Route
Same structure as Facebook, using the Twitter dataset ID.

---

### Modules 301-308: LinkedIn Route
Same structure as Facebook/Twitter, using the LinkedIn dataset ID.

---

### Module 500: Webhook Response
**Type:** `gateway:WebhookRespond`
**Purpose:** Returns a JSON summary when scraping completes.

**Response Format:**
```json
{
  "success": true,
  "message": "Scraping completed successfully",
  "execution_date": "2025-01-16 10:30:00",
  "date_range": {
    "start_date": "01-09-2025",
    "end_date": "01-16-2025",
    "description": "Last 7 days"
  },
  "platforms_scraped": {
    "facebook": 2,
    "twitter": 3,
    "linkedin": 1
  },
  "note": "Check your Google Sheet for detailed results"
}
```

---

## How to Trigger the Scenario

### Option 1: Manual Trigger via HTTP Request

Send a POST request to your webhook URL:

```bash
curl -X POST "YOUR_WEBHOOK_URL" \
  -H "Content-Type: application/json" \
  -d '{
    "facebook_urls": [
      "https://www.facebook.com/Nike",
      "https://www.facebook.com/adidas"
    ],
    "twitter_urls": [
      "https://twitter.com/Nike",
      "https://twitter.com/adidas"
    ],
    "linkedin_urls": [
      "https://www.linkedin.com/company/nike"
    ]
  }'
```

### Option 2: Using Postman

1. Create a new POST request
2. URL: Your webhook URL
3. Body > raw > JSON:
```json
{
  "facebook_urls": ["https://www.facebook.com/Nike"],
  "twitter_urls": ["https://twitter.com/Nike"],
  "linkedin_urls": ["https://www.linkedin.com/company/nike"]
}
```

### Option 3: Integration with Other Tools

Use the webhook URL in:
- Zapier
- n8n
- Custom applications
- Other Make.com scenarios

---

## Scheduling

To run the scenario automatically:

1. Open the scenario in Make.com
2. Click on the **clock icon** in the bottom left
3. Select your schedule:
   - **Daily**: Run once per day
   - **Weekly**: Run once per week
   - **Custom**: Set specific intervals

**Recommended:** Run daily to keep your data current with 7-day rolling window.

**Important:** When scheduling, you'll need to provide default URLs. Edit the webhook module and set default values, or create a separate "scheduled" version that has hardcoded URLs.

---

## Troubleshooting

### Error: "Invalid API Token"
- Verify your Bright Data API token is correct
- Check it hasn't expired
- Ensure it's entered without extra spaces

### Error: "Snapshot not ready" (Status 202)
- The scraping job is still processing
- Increase the Sleep delay from 45 to 60-90 seconds
- For large accounts, may need multiple polling attempts

### Error: "Rate limit exceeded" (Status 429)
- You've hit Bright Data's rate limits
- Wait a few minutes before retrying
- Consider reducing the number of URLs per request

### No data appearing in Google Sheets
1. Check the Errors tab for logged issues
2. Verify your Google Sheets connection is active
3. Confirm the Spreadsheet ID and Sheet names are correct
4. Check the Make.com execution history for errors

### LinkedIn not returning posts
- LinkedIn scraper works best with post URLs, not profile URLs
- Try: `https://www.linkedin.com/posts/username_activity-123456789`
- LinkedIn has stricter rate limits

### Webhook not responding
- Ensure the scenario is **ON** (toggle in top right)
- Check Make.com's execution history
- Verify the webhook URL is correct

---

## Support

For issues with:
- **Make.com**: Check [Make.com Help Center](https://www.make.com/en/help)
- **Bright Data**: Check [Bright Data Docs](https://docs.brightdata.com)
- **This Blueprint**: Contact me through Freelancer.com

---

## Changelog

**v1.0 - Initial Release**
- Facebook, Twitter, LinkedIn scraping
- Google Sheets integration
- Error logging
- Webhook response

---

*Documentation created for Freelancer.com Project #40153500*
