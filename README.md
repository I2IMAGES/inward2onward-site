# Inward2Onward LLC — SMS Verification & Web Infrastructure SOP
**Project:** Twilio Toll-Free SMS Verification + Cloudflare Pages Deployment + GitHub Repo Setup  
**Date:** June 20, 2026  
**Author:** EJ Steele / Inward2Onward LLC  
**Status:** Twilio verification submitted — pending review (5–7 business days)  
**Version:** 1.0

---

## Executive Summary

This document captures the full scope of work completed in a single session to:

1. Build and deploy a production-ready web presence for `inward2onward.com`
2. Create legally compliant SMS consent and policy pages
3. Submit Twilio toll-free verification for (855) 971-1504
4. Establish a clean two-repo GitHub architecture connected to Cloudflare Pages

All work is live and verified as of June 20, 2026.

---

## Problem Statement

**Primary blocker:** Twilio toll-free verification for (855) 971-1504 required a Privacy Policy URL and Terms & Conditions URL pointing to a production domain. The existing URL (`i2o-sms-policy.ai-5b7.workers.dev`) was a Cloudflare Workers dev subdomain — a guaranteed denial signal to carrier reviewers.

**Secondary problem:** No production homepage existed at `inward2onward.com`. The domain was live on Cloudflare DNS but had no A record, no website, and no deployable content.

**Architectural error (documented for training):** Initial advice placed homepage and legal pages in the same Cloudflare Pages project under a subfolder structure (`legal/index.html`). This created an `index.html` naming collision in GitHub that caused multiple failed deployments and wasted significant time. The correct architecture — two separate repos, two separate Pages projects — was the user's original instinct and should have been implemented from the start.

---

## Final Architecture

### Domain Structure
| URL | Content | Repo | Pages Project |
|-----|---------|------|---------------|
| `inward2onward.com` | Homepage + SMS Consent | `I2IMAGES/inward2onward-site` | `inward2onward-site` |
| `inward2onward.com/sms-consent.html` | SMS Opt-In Consent Page | `I2IMAGES/inward2onward-site` | `inward2onward-site` |
| `legal.inward2onward.com/legal.html` | Privacy, Cookie, T&C, SMS Terms | `I2IMAGES/inward2onward-legal` | `inward2onward-legal` |

### DNS Configuration (inward2onward.com — Cloudflare)
| Type | Name | Content | Proxy |
|------|------|---------|-------|
| CNAME | `@` | `inward2onward-site.pages.dev` | ON |
| CNAME | `www` | `inward2onward-site.pages.dev` | ON |
| CNAME | `legal` | `inward2onward-legal.pages.dev` | ON |
| MX | `@` | `mx1.privateemail.com` / `mx2.privateemail.com` | OFF |
| TXT | `@` | `v=spf1 include:spf.privateemail.co ~all` | OFF |
| TXT | `_dmarc` | `v=DMARC1; p=none;` | OFF |
| TXT | `resend._domainkey` | Resend DKIM record | OFF |
| TXT | `send` | `v=spf1 include:amazonses.com ~all` | OFF |
| MX | `send` | `feedback-smtp.us-east-1.amazonses.com` | OFF |

**Critical:** Never touch MX, SPF, DKIM, or DMARC records. Email is live on Namecheap Private Email + Resend.

---

## File Inventory

### Repo 1: `I2IMAGES/inward2onward-site`
```
index.html              — Homepage (canonical I2O design tokens inlined)
sms-consent.html        — SMS opt-in consent page
i2o-design-system.css   — Canonical I2O design system (reference file)
```

### Repo 2: `I2IMAGES/inward2onward-legal`
```
legal.html              — Tabbed policies hub (Privacy v1.1, Cookie, T&C, SMS Terms)
```

---

## SMS Programs Registered with Twilio

### (855) 971-1504 — Toll-Free Verified (pending)

| Program | Frequency | Opt-In Method |
|---------|-----------|---------------|
| Karma SMS — Daily Motivation | ~1/day | Web form at inward2onward.com |
| BridgeBuilder Coaching | 2–4/week | BridgeBuilder enrollment form |
| Cascalote Tech Alerts | 1–4/month | Physical or digital enrollment form |

### Twilio Verification Fields Submitted
- **Use Case Description:** Multi-program SMS — motivation, coaching, senior tech alerts
- **Sample Message:** Karma SMS daily motivation message
- **Email for Notifications:** admin@inward2onward.com
- **Opt-In Confirmation:** "You're subscribed to Inward2Onward SMS. Msg freq varies by program. Msg & data rates may apply. Reply STOP to cancel, HELP for help. inward2onward.com/sms-consent.html"
- **Help Message Sample:** "Inward2Onward LLC SMS. Msg & data rates may apply. Reply STOP to cancel. Help: admin@inward2onward.com or 623.272.8066. inward2onward.com/sms-consent.html"
- **Privacy Policy URL:** https://legal.inward2onward.com/legal.html
- **Terms & Conditions URL:** https://legal.inward2onward.com/legal.html
- **Opt-In Keywords:** None (web form enrollment)
- **Age Gated Content:** No

---

## Privacy Policy — Disclosed Data Processors (v1.1)

All eight processors are documented in `legal.html` Section 4:

| Processor | Purpose |
|-----------|---------|
| Stripe, Inc. | Payment processing |
| Google LLC | Website analytics (pseudonymized) |
| Keap / Thryv | CRM and marketing automation |
| Formspree, Inc. | Web form processing |
| Resend, Inc. | Transactional email delivery |
| Supabase, Inc. | Database and backend infrastructure |
| Twilio, Inc. | SMS message delivery |
| Cloudflare, Inc. | Network infrastructure, DNS, CDN, DDoS protection |

---

## Design System

### Canonical File: `i2o-design-system.css` (v1.0)

**Rule:** All I2O web builds reference canonical CSS variables. No hardcoded hex values in page-level styles.

### Key Tokens
| Token | Value | Use |
|-------|-------|-----|
| `--i2o-navy` | `#0a0f1e` | Page background (dark surfaces) |
| `--i2o-blue` | `#3B82F6` | Primary brand blue |
| `--i2o-indigo` | `#6366F1` | Accent / gradient end |
| `--i2o-border` | `#1e3a5f` | All borders on dark surfaces |
| `--i2o-s1` | `#f1f5f9` | Primary text |
| `--i2o-s2` | `#94a3b8` | Secondary text |
| `--i2o-s3` | `#475569` | Tertiary / muted text |
| `--i2o-font-display` | Barlow Condensed | Headlines, labels, buttons |
| `--i2o-font-body` | Inter | Body copy |

### Brand Tagline (current)
> Built Right. Accountable Throughout.

### Proof Line (verified)
> 325+ CRM and automation implementations. One standard: it has to work.

*Note: 325+ is a verified conservative figure based on EJ Steele's 4-year tenure at Keap/Thryv as onboarding coach, internal/external coach, and implementation strategy manager.*

---

## Cookie Consent Banner

Implemented on all three public pages. Behavior:

- Fires on first visit if no prior decision in `localStorage`
- Key: `i2o_cookie_consent` — values: `accepted` or `declined`
- Two buttons: Accept all / Decline non-essential
- Decision persists across sessions

**Open item:** Decline path does not yet disable Google Analytics. GA is not installed. Wire GA disable to decline path when GA measurement ID is added.

---

## Standard Operating Procedures

### SOP 1 — Updating the Homepage

1. Edit `index.html` in `I2IMAGES/inward2onward-site` on GitHub
2. Commit to `main`
3. Cloudflare Pages auto-deploys within ~30 seconds
4. Verify at `https://inward2onward.com`

### SOP 2 — Updating Legal Policies

1. Edit `legal.html` in `I2IMAGES/inward2onward-legal` on GitHub
2. Commit to `main`
3. Cloudflare Pages auto-deploys within ~30 seconds
4. Verify at `https://legal.inward2onward.com/legal.html`
5. Update the effective date and version number in the relevant policy tab

### SOP 3 — Adding a New SMS Program

1. Add program description to `legal.html` SMS Terms section (Section 1–2)
2. Update message frequency disclosure
3. Prepare four fields for Twilio:
   - Use Case Description
   - Sample Message
   - Opt-In Confirmation Message
   - Help Message Sample
4. Submit to Twilio via Console → Numbers & Senders → phone number → Configure → Verify

### SOP 4 — Adding a New Data Processor

1. Open `legal.html` in `I2IMAGES/inward2onward-legal`
2. Locate Section 4: Third-Party Service Providers
3. Add new `<li>` entry with: processor name, purpose, what data they touch, link to their privacy policy
4. Update effective date to current date and increment version (e.g. v1.1 → v1.2)
5. Commit to `main`

### SOP 5 — Deploying a New Page

1. Create the HTML file — standalone, self-contained, design tokens inlined from `i2o-design-system.css`
2. Add cookie consent banner snippet before `</body>`
3. Update all links — internal pages use absolute URLs (`https://inward2onward.com/page.html`)
4. Upload to `I2IMAGES/inward2onward-site`
5. Commit to `main` — auto-deploys

### SOP 6 — DNS Changes

**Always check before touching DNS:**
- MX records → email is live, do not touch
- SPF/DKIM/DMARC records → do not touch
- Only add/modify CNAME records for new web properties

---

## Troubleshooting Guide

### Site not updating after GitHub commit
- Check Cloudflare Pages → Deployments tab for build status
- If no new deployment triggered: go to Pages project → Create deployment → select branch `main`

### `legal.inward2onward.com` returning 404
- Verify `legal.html` exists in `I2IMAGES/inward2onward-legal` root
- Check Cloudflare Pages `inward2onward-legal` → Custom domains → confirm `legal.inward2onward.com` shows Active + SSL enabled

### Email addresses showing as `[email protected]` on pages
- Cloudflare email obfuscation is triggering
- Fix: encode `@` as `&#64;` in both `href` and visible text
- Example: `<a href="mailto:admin&#64;inward2onward.com">admin&#64;inward2onward.com</a>`
- Do NOT disable Scrape Shield zone-wide

### Twilio verification denied
- Check that both URLs resolve: `https://legal.inward2onward.com/legal.html`
- Confirm opt-in language on `sms-consent.html` matches what was submitted
- Review denial reason in Twilio Console → Regulatory Information tab
- Resubmit with clarifications — common ask is more detail on opt-in method

---

## Open Items (as of June 20, 2026)

| Item | Priority | Owner |
|------|----------|-------|
| Twilio verification approval | Blocker for Karma SMS launch | Twilio (5–7 days) |
| Google Analytics installation + GA disable wired to cookie banner decline | Medium | EJ |
| DMARC policy tightened from `none` to `quarantine` | Low | EJ |
| Grant Agent SOP (`SOPgrantagent.md`) — spotlight strategy TBD | Medium | EJ + Claude |
| NMET / minority-owned business positioning on website | Medium | EJ + Claude |
| Real homepage to replace placeholder | High (next build) | EJ + Claude |

---

## Lessons Learned

1. **Two repos from the start.** Homepage and legal pages are separate concerns with separate update cadences. They belong in separate repos. When a user's instinct aligns with clean architecture, follow it.

2. **Flat files beat folder structures** in static site deployments. `legal.html` at root is unambiguous. `legal/index.html` inside a subfolder creates naming collisions and confusion.

3. **Subdomains beat path routing** for separate Pages projects. `legal.inward2onward.com` requires one CNAME. Routing two Pages projects to the same domain at different paths requires a Worker. Always choose the simpler architecture.

4. **DNS audit before any deployment.** Knowing that Cloudflare was already the nameserver provider before touching anything would have saved 30 minutes.

5. **Verify URLs before submitting to Twilio.** A `.workers.dev` or `.pages.dev` URL in the Privacy Policy field is an automatic denial signal. Production domain only.

---

## Reference — Twilio Copy Bank

### Opt-In Confirmation (all programs)
```
You're subscribed to Inward2Onward SMS. Msg freq varies by program. Msg & data rates may apply. Reply STOP to cancel, HELP for help. inward2onward.com/sms-consent.html
```

### Help Response (all programs)
```
Inward2Onward LLC SMS. Msg & data rates may apply. Reply STOP to cancel. Help: admin@inward2onward.com or 623.272.8066. inward2onward.com/sms-consent.html
```

### Karma SMS Sample Message
```
You don't need a perfect plan. You need a decision you can act on right now. One step forward is still forward. — Karma SMS by Inward2Onward | Reply STOP to cancel
```

### BridgeBuilder Sample Message
```
Your BridgeBuilder session is tomorrow at 2 PM. Review your Week 3 action items before we meet. Questions? Reply here. — Inward2Onward | Reply STOP to cancel
```

### Cascalote Sample Message
```
Cascalote Tech Alert: Your Aqara doorbell battery is running low. A Doorbell Health Check is recommended. Call or text EJ at 623.272.8066 to schedule. — Inward2Onward | Reply STOP to cancel
```

---

*Document generated: June 20, 2026 | Inward2Onward LLC | admin@inward2onward.com | 623.272.8066*
