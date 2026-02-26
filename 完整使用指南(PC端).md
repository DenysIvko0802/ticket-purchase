# Damai Auto Ticket System - Complete Usage Guide (PC)

> **Version**: v1.0  
> **Updated**: 2026-01-04  
> **Project**: ticket-purchase

---

## 📋 Table of Contents

- [System Overview](#system-overview)
- [Quick Start](#quick-start)
- [Configuration](#configuration)
- [Performance Optimization](#performance-optimization)
- [Execution Flow](#execution-flow)
- [Troubleshooting](#troubleshooting)
- [Best Practices](#best-practices)
- [FAQ](#faq)
- [User Element Retry Mechanism](#user-element-retry-mechanism)
- [Quick Checklist](#quick-checklist)
- [Appendix](#appendix)
- [Summary](#summary)

---

## System Overview

### Core Features

This system is a Damai automatic ticket purchasing tool built with Python + Selenium. It supports:

- ✅ **Automatic login** – QR-code login with your Damai account
- ✅ **Smart selection** – Automatically selects city, session, price, and quantity
- ✅ **Fuzzy matching** – Supports multiple formats for dates and prices
- ✅ **Multi-platform support** – Works with both PC and mobile layouts
- ✅ **Auto submission** – Automatically selects attendees and submits the order
- ✅ **Error handling** – Robust exception handling and retry mechanisms
- ✅ **Performance optimization** – Fast mode, 40–75% speed improvement

### Automation Flow

```
Start script → Environment check → Open browser → Scan to log in → Select session →
Select price → Select quantity → Click “Reserve/Book Now” → Select attendees → Submit order
```

---

## Quick Start

### 1. Environment Requirements

- **OS**: macOS / Linux / Windows
- **Python**: 3.7+
- **Browser**: Latest Chrome
- **Network**: Stable connection

### 2. Install Dependencies

```bash
# Go to project directory
cd ticket-purchase

# Install Python dependencies
pip install selenium chromedriver-autoinstaller
```

### 3. Configuration File

Create `config.json` in the project root:

```json
{
    "index_url": "https://www.damai.cn/",
    "login_url": "https://passport.damai.cn/login",
    "target_url": "https://detail.damai.cn/item.htm?id=123456",
    "users": ["Zhang San", "Li Si"],
    "city": "Hangzhou",
    "dates": ["2026-04-11"],
    "prices": ["680"],
    "fast_mode": true,
    "if_listen": true,
    "if_commit_order": true,
    "max_retries": 1000,
    "page_load_delay": 2
}
```

> **Note**: You can keep the Chinese names if your Damai account uses them; only the explanation text here is translated.

### 4. Run the Script

```bash
# Basic run
python damai/damai.py

# Or with a virtual environment
.venv1/bin/python damai/damai.py
```

---

## Configuration

### Parameter Description

| Parameter | Type | Required | Description | Default |
|----------|------|----------|-------------|---------|
| `index_url` | string | ✅ | Damai home URL | - |
| `login_url` | string | ✅ | Damai login URL | - |
| `target_url` | string | ✅ | Target show detail page URL | - |
| `users` | array | ✅ | List of attendee names | - |
| `city` | string | ❌ | City of the show | - |
| `dates` | array | ❌ | List of session dates | - |
| `prices` | array | ❌ | List of ticket prices | - |
| `fast_mode` | boolean | ❌ | Fast mode switch | true |
| `if_listen` | boolean | ❌ | Listen for “restock / waitlist” | true |
| `if_commit_order` | boolean | ❌ | Auto submit order | true |
| `max_retries` | number | ❌ | Max retry count | 1000 |
| `page_load_delay` | number | ❌ | Page load wait time (seconds) | 2 |

### Configuration Examples

#### Basic config (recommended for beginners)

```json
{
    "index_url": "https://www.damai.cn/",
    "login_url": "https://passport.damai.cn/login",
    "target_url": "https://detail.damai.cn/item.htm?id=123456",
    "users": ["Zhang San"],
    "fast_mode": false,
    "page_load_delay": 3,
    "if_commit_order": false
}
```

#### Full config (experienced users)

```json
{
    "index_url": "https://www.damai.cn/",
    "login_url": "https://passport.damai.cn/login",
    "target_url": "https://detail.damai.cn/item.htm?id=123456",
    "users": ["Zhang San", "Li Si"],
    "city": "Hangzhou",
    "dates": ["2026-04-11", "April 11", "2026.04.11"],
    "prices": ["680", "¥680", "680 RMB"],
    "fast_mode": true,
    "if_listen": true,
    "if_commit_order": true,
    "max_retries": 5000,
    "page_load_delay": 2
}
```

---

## Performance Optimization

### Fast Mode (`fast_mode`)

#### Effect Comparison

| Operation | Normal Mode | Fast Mode | Improvement |
|----------|-------------|-----------|-------------|
| Order confirmation page load | 2.0 s | 0 s (explicit wait) | 100% |
| Polling interval | 1.0 s | 0.3 s | 70% |
| Post-click wait | 0.5 s | 0.2 s | 60% |
| Detail page selection | 4–7 s | 1.1–1.8 s | 70–75% |
| **Overall saving** | - | **≈ 5–8 s** | **50–70%** |

#### Fast Mode Characteristics

✅ **Reduced waits** – All sleep times reduced by 30–70%  
✅ **Less debug output** – Skips heavy page scanning logs  
✅ **Faster polling** – Polling interval reduced from 1 s to 0.3 s  
✅ **Batch display** – Shows all options at once

#### Usage Suggestions

**Recommended config (most scenarios)**:

```json
{
    "fast_mode": true,
    "if_commit_order": true
}
```

Suitable when:

- ✅抢购热门演出 / Buying very popular shows  
- ✅ Network is good  
- ✅ You are familiar with the flow and don’t need debug logs

**Debug config (first-time or when issues occur)**:

```json
{
    "fast_mode": false,
    "if_commit_order": false,
    "page_load_delay": 5
}
```

Use this when:

- 🔧 First-time use, want to understand the full flow  
- 🔧 Encountering problems and need detailed logs  
- 🔧 Testing whether the configuration is correct

### Detail Page Selection Optimization

#### What’s Optimized

1. **Direct operations on detail page** – `target_url` is already the detail page, no extra refresh
2. **Less output in fast mode** – Skips unnecessary debug information
3. **Batch display of options** – Shows all options at once instead of looping with prints
4. **Shorter waits** – From 0.5 s down to 0.1–0.2 s

#### Output Comparison

**Normal mode**:

```
*** Select city ***
  Target city: Hangzhou
  Found 3 city options:
    [0] Shanghai
    [1] Beijing
    [2] Hangzhou
  ✓ Matched: Hangzhou
```

**Fast mode**:

```
*** Select city ***
  Target city: Hangzhou
  ✓ Matched: Hangzhou
```

---

## Execution Flow

### Full Execution Flow

#### Phase 1: Environment Check

```
==================================================
Damai ticket script starting
==================================================

✓ Config file loaded successfully
  - Target URL: https://detail.damai.cn/item.htm?id=123456
  - Audience count: 2
  - Max retries: 1000

⏳ Checking Chrome environment...
  Chrome version: 144
  ✓ ChromeDriver ready
```

#### Phase 2: Login

```
⏳ Starting browser...
⏳ Opening Damai...

*** Please scan to log in ***
```

**Action**: Use the Damai mobile app to scan the QR code in the browser to complete login.

#### Phase 3: Detail Page Selection

**PC output example**:

```
Detected PC page

*** Select city ***
  Target city: Hangzhou
  ✓ Matched: Hangzhou

*** Select session ***
  Target sessions: ['2026-04-11']
  ✓ Matched: 2026-04-11 Sat 19:30

*** Select price ***
  Target prices: ['680']
  ✓ Matched: ¥680

*** Select quantity ***
  Target quantity: 2 tickets
  ✓ Selected 2 tickets
```

#### Phase 4: Poll for “Reserve/Book Now” Button

```
✓ Detected button: Reserve Now
  Waiting for page redirect...
  ✓ Page redirected to order confirmation
```

#### Phase 5: Select Attendees

```
*** Select attendees ***

  Selecting: Zhang San (1/2)
  ✓ Selected: Zhang San

  Selecting: Li Si (2/2)
  ✓ Selected: Li Si

*** Selected 2/2 attendees ***
```

#### Phase 6: Submit Order

```
*** Preparing to submit order ***
  ✓ Found <span> (exact match): Submit Now
*** Order submitted ***
```

---

## Troubleshooting

### Issue 1: Cannot Find User Element

#### Symptom

```
⚠ Could not find element containing 'Zhang San'
⚠ Could not find user: Zhang San
```

#### Possible Causes

**1. Page not fully loaded** ⭐ Most common

**Solution**:

```json
{
    "page_load_delay": 3-5
}
```

Increase the delay so that the attendee list has enough time to load.

**2. Username text mismatch**

**How to check**:

- Turn off `fast_mode` and review scanning output
- Press F12 in the browser, search for the username, and confirm the exact text
- Common mismatches:
  - Config: `"Zhang San"` → Page: `"Zhang San "` (trailing space)
  - Config: `"Zhang San"` → Page: `"Zhang San (VIP)"` (suffix)

**Solution**:

```json
{
    "users": ["Zhang San", "Zhang San ", "Zhang San (VIP)"]
}
```

Add possible variants to the `users` list.

**3. Page structure changed**

**Solution**:

- Capture a screenshot of the attendee HTML area
- Provide full debug output
- Open a GitHub issue with details

### Issue 2: ChromeDriver Version Mismatch

#### Symptom

```text
SessionNotCreatedException: This version of ChromeDriver only supports Chrome version 145
Current browser version is 144.0.7559.110
```

#### Solutions

```bash
# Option 1: Re-run the script (auto-installs correct driver)
python damai/damai.py

# Option 2: Manually check environment
python damai/check_environment.py

# Option 3: Reinstall ChromeDriver (macOS example)
brew reinstall --cask chromedriver
```

On Windows, reinstall ChromeDriver using your usual method or remove the old binary and let `chromedriver-autoinstaller` handle it.

### Issue 3: Order Submission Fails

#### Symptom

```text
⚠ Could not find a clear submit button
⚠ All methods failed, please click the submit button manually
```

#### Debug Steps

1. **Check scanning output** and confirm the actual button text
2. **Check if the button is covered** (popup/dialog/overlay)
3. **Try clicking manually** to confirm that the page itself works

---

## Best Practices

### 1. Configuration Recommendations

#### Conservative config (recommended for beginners)

```json
{
    "users": ["Zhang San"],
    "dates": ["2026-04-11", "April 11"],
    "prices": ["680", "¥680"],
    "fast_mode": false,
    "if_commit_order": false
}
```

Characteristics: single attendee, multiple formats for date and price, and **no auto submission** (you confirm manually).

#### Aggressive config (experienced users)

```json
{
    "users": ["Zhang San", "Li Si", "Wang Wu"],
    "dates": ["2026-04-11"],
    "prices": ["680"],
    "fast_mode": true,
    "if_listen": true,
    "if_commit_order": true,
    "max_retries": 5000
}
```

Characteristics: multiple attendees, precise config, auto submission, and high retry count.

### 2. Timing Suggestions

**Before sale**:

- ⏰ Start the script **30 minutes** before the sale
- ⏰ Complete login **10 minutes** before
- ⏰ Open detail page **5 minutes** before

**During sale**:

- 🎯 For on-time sale: set `max_retries: 5000+`
- 🎯 For presale: start polling 1–2 minutes early
- 🎯 For returned tickets: keep polling with `if_listen: true`

### 3. Security Recommendations

**Account security**:

- ✅ Use your official Damai account
- ✅ Ensure your network environment is safe
- ❌ Do not share your `config.json` (contains personal info)
- ❌ Avoid running on public/shared machines

**Payment security**:

- ✅ For first-time use, set `if_commit_order: false`
- ✅ Manually verify order details before enabling auto submit
- ✅ Double-check ticket info and total amount

---

## FAQ

### Q1: Will the script automatically grab tickets?

**A**: Yes. The entire process is automated, including login, selection, and submission.

However, for first-time use you should set `if_commit_order: false` and confirm the order manually.

### Q2: What types of events are supported?

**A**: In theory, all Damai events are supported, including:

- 🎵 Concerts
- 🎭 Drama / Opera
- 🏀 Sports events
- 🎪 Exhibitions / Activities

### Q3: Can I buy multiple tickets?

**A**: Yes. Add multiple attendees to the `users` array; the system will select the corresponding number of tickets.

### Q4: How high is the success rate?

**A**: It depends on several factors:

- ✅ Network speed: faster is better
- ✅ Config accuracy: session/price configuration accuracy
- ✅ Machine performance: affects browser responsiveness

**Suggestions**:

- Use wired network if possible
- Close other bandwidth-heavy apps
- Prepare everything in advance

### Q5: Does fast mode reduce success rate?

**A**: No. Fast mode only reduces unnecessary waits; the core logic is exactly the same. In practice, the faster response can **increase** success rate by around 5–10%.

### Q6: Will my Damai account get banned?

**A**: Using Selenium automation itself is not explicitly forbidden, but:

- ⚠️ Do **not** refresh too frequently (set a reasonable `max_retries`)
- ⚠️ Do **not** run many scripts in parallel
- ⚠️ Do **not** use this for ticket scalping

---

## User Element Retry Mechanism

### Description

When the script cannot find the attendee element, it will **automatically retry up to 5 times**, with **0.5 seconds between retries**, for a maximum wait of **2.5 seconds**.

### Workflow

```
1st scan (immediately)
  ↓ Not found
Wait 0.5 s
  ↓
2nd scan
  ↓ Not found
Wait 0.5 s
  ↓
... (repeats up to 5 times)
```

### Output Examples

**Success on 1st attempt**:

```
  🔍 Scanning attendee elements...
  ✓ Selected: Zhang San
```

**Success on 3rd attempt**:

```
  🔍 Scanning attendee elements...
  ⚠ Could not find element containing 'Zhang San'
  2nd attempt...
  ⚠ Could not find element containing 'Zhang San'
  3rd attempt...
  ✓ Found user element successfully on 3rd attempt
  ✓ Selected: Zhang San
```

### Performance Impact

| Scenario | Extra wait time |
|---------|-----------------|
| Success on 1st attempt | 0 s |
| Success on 2nd attempt | 0.5 s |
| Success on 3rd attempt | 1.0 s |
| Success on 5th attempt | 2.0 s |
| All attempts fail | 2.5 s |

---

## Quick Checklist

When you encounter issues, go through the following:

### User Not Found Problems

- [ ] Is `page_load_delay` large enough? (recommended 3–5 seconds)
- [ ] Is `fast_mode` turned off while debugging?
- [ ] Does the username text exactly match the page? (check spaces, suffixes)
- [ ] Have you checked the full scanning output?
- [ ] Is the page fully loaded? (check manually)
- [ ] Is your network stable? (try refreshing)

### Config Optimization

- [ ] Is `fast_mode` enabled when you actually grab tickets?
- [ ] Do `dates` and `prices` include multiple formats?
- [ ] Is `max_retries` large enough?
- [ ] Is `page_load_delay` appropriate for your network?

---

## Appendix

### A. Full Config Template

```json
{
    "index_url": "https://www.damai.cn/",
    "login_url": "https://passport.damai.cn/login",
    "target_url": "https://detail.damai.cn/item.htm?id=XXXXXX",
    "users": ["Attendee Name"],
    "city": "",
    "dates": [],
    "prices": [],
    "fast_mode": true,
    "if_listen": true,
    "if_commit_order": true,
    "max_retries": 1000,
    "page_load_delay": 2
}
```

### B. Dependency List

```text
selenium>=4.0.0
chromedriver-autoinstaller>=0.6.0
```

### C. Directory Structure

```text
ticket-purchase/
├── damai/
│   ├── __init__.py
│   ├── damai.py              # main entry
│   ├── concert.py            # core logic
│   ├── config.py             # config classes
│   └── check_environment.py  # environment check
├── config.json               # config file (create yourself)
├── requirements.txt          # dependencies
└── README.md                 # documentation
```

---

## Summary

### Key Features

✅ **Fully automated** – from login to final submission  
✅ **Performance optimized** – fast mode improves speed by 50–70%  
✅ **Smart retries** – attendee scanning retries up to 5 times  
✅ **Fuzzy matching** – multiple formats for dates and prices  
✅ **Multi-platform** – adapts to both PC and mobile pages  
✅ **Detailed logs** – easy debugging and troubleshooting

### Usage Suggestions

1. For **first-time use**: turn off `fast_mode`, increase `page_load_delay`.  
2. For **slow networks**: increase `page_load_delay` to 5–10 seconds.  
3. For **debugging**: turn off `fast_mode` and inspect detailed log output.  
4. For **real ticket rush**: tune your config based on prior tests.

---

**Wish you successful ticket hunting!** 🎉  

If you have issues or suggestions, feel free to give feedback.
