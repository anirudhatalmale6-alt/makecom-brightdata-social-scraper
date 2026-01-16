# Bright Data Social Scraper V3 - Google Sheets Input

## Overview

This Make.com scenario reads profile URLs from your Google Sheet and scrapes posts from LinkedIn, Twitter/X, and Facebook.

---

## Your Google Sheet Structure

### Tab 1: "Leads" (Input)

| Column | Header | Example |
|--------|--------|---------|
| A | Lead ID | 1 |
| B | LinkedIn URL | https://www.linkedin.com/in/williamhgates/ |
| C | Twitter URL | https://twitter.com/BillGates |
| D | Facebook URL | https://www.facebook.com/BillGates |
| E | Status | (auto-filled: "Scraped - 2025-01-16 10:30") |

### Tab 2: "Posts" (Output)

Create this tab with these headers in Row 1:

| Column | Header |
|--------|--------|
| A | Lead ID |
| B | Platform |
| C | Post ID |
| D | Post URL |
| E | Author |
| F | Content |
| G | Timestamp |
| H | Hashtags |
| I | Likes |
| J | Comments |
| K | Shares |
| L | Scraped At |

### Tab 3: "Errors" (Error Log)

Create this tab with these headers in Row 1:

| Column | Header |
|--------|--------|
| A | Timestamp |
| B | Lead ID |
| C | Platform |
| D | URL Attempted |
| E | Status Code |
| F | Error Message |
| G | Snapshot ID |

---

## How It Works

1. **Reads each row** from your "Leads" sheet
2. **For each lead**, scrapes posts from all 3 platforms (if URL exists)
3. **Writes posts** to the "Posts" tab with Lead ID linked
4. **Updates Status** column in Leads sheet to "Scraped - [date/time]"
5. **Logs errors** to the "Errors" tab

---

## Setup Steps

### 1. Import Blueprint

1. Go to Make.com > Scenarios > Create New
2. Click ⋮ (More) > Import Blueprint
3. Upload `brightdata-social-scraper-v3-sheets-input.json`

### 2. Get Bright Data API Key

1. Sign up at https://brightdata.com (free trial available)
2. Go to Account Settings > API Tokens
3. Create new token and copy it

### 3. Configure the Scenario

**Module 1 - Read Leads:**
- Connect your Google account
- Select your spreadsheet
- Select the "Leads" sheet

**Module 2 - Set Variables:**
- Replace `__YOUR_BRIGHTDATA_API_KEY__` with your actual API token

**All Google Sheets modules:**
- Select the same spreadsheet
- Posts modules → Select "Posts" sheet
- Errors modules → Select "Errors" sheet
- Update Status module → Select "Leads" sheet

---

## Placeholders to Replace

| Placeholder | Replace With |
|-------------|--------------|
| `__GOOGLE_CONNECTION__` | Your Google account connection |
| `__YOUR_SPREADSHEET_ID__` | Your Google Sheet ID |
| `__LEADS_SHEET_ID__` | "Leads" tab name or ID |
| `__POSTS_SHEET_ID__` | "Posts" tab name or ID |
| `__ERRORS_SHEET_ID__` | "Errors" tab name or ID |
| `__YOUR_BRIGHTDATA_API_KEY__` | Your Bright Data API token |

---

## Running the Scenario

### Manual Run
1. Click "Run once" in Make.com
2. The scenario processes all leads in your sheet

### Scheduled Run
1. Click the clock icon
2. Set schedule (e.g., daily at 9 AM)
3. Turn ON the scenario

---

## Notes

- **Processing time**: Each lead takes ~3 minutes (60s wait per platform)
- **Rate limits**: Bright Data has usage limits on free tier
- **Empty URLs**: If a platform URL is empty, that platform is skipped
- **Status column**: Automatically updated after processing each lead

---

## Troubleshooting

**"Snapshot not ready" errors:**
- Increase the Sleep delay from 60 to 90 seconds

**No posts returned:**
- Verify the profile URL is correct and public
- Check if the profile has posts in the last 7 days

**Google Sheets permission error:**
- Re-authenticate your Google connection
- Make sure the sheet is not restricted

---

*Created for Freelancer Project #40153500*
