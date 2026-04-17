# RiderShield: Phase 3 — Complete Evaluation Guide

This document is written specifically for hackathon judges and technical evaluators. Every single feature of the RiderShield platform is documented below with exact steps, credentials, expected outputs, and API examples so that every component of the system can be independently verified without any ambiguity.

---

## Live System URLs

| Service | URL |
|---|---|
| Public Website | https://ridershieldpublic.vercel.app |
| Admin Dashboard | https://ridershieldadmin.vercel.app |
| Backend API | https://ridershield-kxj0.onrender.com |
| ML Service | https://ridershield-ml.onrender.com |
| GitHub Repository | https://github.com/aadiexii/RIDERSHIELD |

> The backend and ML service are on Render free tier. They may take up to 50 seconds to wake up after inactivity. If an API call fails the first time, wait 60 seconds and try again.

---

## Part 1: Public Website — ridershieldpublic.vercel.app

### 1.1 Landing Page
Navigate to https://ridershieldpublic.vercel.app

**What you will see:**
- A full-screen hero section with the tagline "When the City Stops You, We Make Sure Your Income Doesn't"
- A live Pincode Coverage Checker input box
- An animated statistics ticker (10M+ workers, Rs. 0 manual claims, 2 Min avg payout)
- A scrolling feature breakdown of the parametric insurance concept

**Test the Pincode Checker:**
- Type `110001` (Delhi) → You will see "Zone Covered" in green
- Type `201301` (Noida) → You will see "Zone Covered" in green
- Type `400001` (Mumbai) → You will see "Join Waitlist" in orange
- This dynamically checks if the entered pincode falls within our covered NCR zones

**The "Get Started" Button:**
- Click it → A worker registration modal appears
- This is the conversion funnel for new delivery workers

### 1.2 Docs Page
Navigate to https://ridershieldpublic.vercel.app/docs

**What you will see:**
- A full sidebar documentation portal similar to Stripe or Tailwind's documentation
- Navigation categories: Hackathon Demo Mode, Getting Started, Setup & Registration, Coverage & Payouts, Help & FAQs

**Test navigation:**
- Click "E2E Demo Guide" in the sidebar → This is the in-browser guide built for hackathon judges
- Click "What is RiderShield?" → Complete product overview
- Click "How Payouts Work" → Detailed parametric payout explanation
- Click "Safety Mode" → Crowdsourced disruption reporting feature explained
- Click "No-Claim Reback" → 15% cashback reward system explained
- Click "AI Support Bot" → Explanation of the Gemini-powered chatbot
- Click "FAQs" → Common worker questions answered

**Verify the sidebar is functional:**
- The sidebar collapses and expands using the panel toggle icon
- Previous / Next navigation buttons appear at the bottom of each doc page
- All 12+ documentation sections are fully written, not placeholder text

### 1.3 AI Support Bot
On any page of the public website, look for the floating chat button in the bottom right corner.

**What it does:**
- Powered by Google Gemini 1.5 Flash
- Answers worker queries about coverage, payouts, premiums, and zones
- Supports both English and Hindi queries

**Test queries to ask:**
- "What is parametric insurance?" → Should explain the zero-touch payout concept
- "How much will I get paid during a flood?" → Should explain severity-based payouts
- "Is my area covered?" → Should ask for pincode and check coverage
- "Mera payout kab aayega?" (Hindi) → Should respond in Hindi about payout timing

---

## Part 2: Admin Dashboard — ridershieldadmin.vercel.app

### 2.1 Login
Navigate to https://ridershieldadmin.vercel.app

**Demo Credentials:**
| Role | Email | Password |
|---|---|---|
| Analyst | analyst@ridershield.in | analyst123 |

Enter the credentials and click Login. A JWT token is issued by the backend and stored in the session.

**What you will see after login:**
- A full admin dashboard with a dark sidebar navigation
- Real-time metrics: Total Claims, Active Workers, Total Payout Amount, Fraud Prevented

### 2.2 Dashboard (Home)
The main dashboard at `/dashboard` shows:
- Live claim statistics pulled from MongoDB
- A summary of all claims processed, pending, and paid
- Quick action buttons to navigate to key sections

### 2.3 Claims Management
Click "Claims" in the sidebar.

**What you will see:**
- A paginated table of every parametric claim ever processed
- Each row shows: Claim ID, Worker ID, Disruption Type, Severity Score, Payout Amount, Fraud Risk Level (Low / Medium / High), Status (approved / paid / rejected), Timestamp
- A "Review" button on each claim to see the full fraud detection breakdown

**Verify fraud scoring is real:**
- Click any claim → You will see a modal with the 5-signal fraud check results:
  - Claim Velocity: pass/fail with reason
  - Trust Score: the worker's current score
  - Time Pattern: whether the claim was within working hours
  - GPS Zone Verification: whether the worker was physically in the zone
  - Device Fingerprint: whether the device matches the registered profile

### 2.4 Workers Registry
Click "Workers" in the sidebar.

**What you will see:**
- A table of all registered workers with their Worker ID, phone number, zone, trust score, and plan type
- The demo worker registered as W-XXXX with phone 9999999999 will appear here after the mobile app is tested

### 2.5 Zone Map
Click "Zone Map" in the sidebar.

**What you will see:**
- An interactive map of NCR India showing all 3 covered zones:
  - Noida Sector 18 (Zone Z001)
  - Delhi Rohini (Zone Z002)
  - Gurugram Sector 45 (Zone Z003)
- Each zone is color-coded by current risk score
- Hover over a zone to see live weather stats and current severity

### 2.6 Analytics
Click "Analytics" in the sidebar.

**What you will see:**
- Total payouts distributed (in Rs.)
- Fraud attempts detected and blocked
- Premium collection totals
- Disruption frequency by type (rain, flood, heat, smog, curfew)
- All figures pull from the live MongoDB database

### 2.7 Simulation Studio
Click "Orchestration Hub" → "Simulation Studio" in the sidebar. This is the most important section for evaluation.

**What it lets you do:**
Manually trigger any climate disruption event in any registered zone. This is how the real-time E2E demo works.

**How to trigger a disruption:**
1. Select Zone: "Noida Sector 18" (Z001) — this is where the demo worker lives
2. Select Disruption Type: "Flood" (recommended for the highest visual impact)
3. Set Severity: 0.85 (high severity, will result in approximately Rs. 300+ payout)
4. Click "Trigger Disruption"

**What happens on the backend (verifiable in logs):**
1. The backend calls the Python ML service at `https://ridershield-ml.onrender.com/predict` with the disruption parameters
2. The ML model (RandomForestRegressor) calculates the exact payout based on severity, hours affected, and earnings baseline
3. The fraud detection engine runs 5 simultaneous checks
4. If approved, a new Claim document is created in MongoDB
5. The disruption is added to the `activeAlerts` in-memory array
6. The Worker App (if open and polling) picks up the alert within 10 seconds

**What you see on the Admin screen after triggering:**
- A success response showing: Disruption Type, Severity Score, ML-calculated Payout Amount, Claim ID, Fraud Risk Level, Confidence Score
- The new claim immediately appears in the Claims table

### 2.8 Manual Pulse (Trigger CRON)
In the Orchestration Hub, there is a "Trigger Detection Pulse" button.

**What it does:**
- Manually fires the CRON detection logic once immediately
- The CRON normally runs on a 10-minute schedule automatically
- Pressing this forces it to run now: it hits OpenWeatherMap, WAQI, and NewsAPI for all 3 zones, processes signals, runs fraud detection, and creates claims if thresholds are breached
- You can verify this in the backend terminal logs: `[CRON] Running multi-signal disruption detection...` will appear

### 2.9 Rewards Management
Click "Rewards" in the sidebar.

**What you will see:**
- A system for managing No-Claim Streak cashbacks
- Workers who complete 4 consecutive weeks without a Safety Mode claim receive 15% of their premiums back
- The admin can view streaks and manually advance them for testing

---

## Part 3: Worker Mobile App — via Expo Go

### 3.1 Installation
1. Install "Expo Go" from the App Store or Play Store on your physical phone
2. Open a terminal in `apps/worker-app/` and run: `npx expo start`
3. Scan the QR code with Expo Go (Android) or Camera (iOS)
4. Ensure your phone and laptop are on the same WiFi network
5. Set `EXPO_PUBLIC_API_URL` in `apps/worker-app/.env` to your laptop's LAN IP (e.g., `http://192.168.x.x:5000`)

### 3.2 Onboarding — Phone Screen
The first screen you see is the phone number input.

**Hackathon Bypass Banner:**
- A blue banner at the top clearly shows: "Phone: 9999999999 | OTP: 123456"
- Enter `9999999999` in the phone number field
- Click "Send OTP"

**What happens:**
- The app sends a verification request to the backend
- The backend intercepts the demo phone number and bypasses real Firebase SMS
- You are directed to the OTP screen immediately

### 3.3 Onboarding — OTP Screen
- Enter `123456` as the OTP
- Click "Verify"

**What happens on the backend:**
- The backend receives the token `DEMO_HACKATHON_TOKEN_123`
- It checks if phone is `9999999999`, skips Firebase token verification
- A new Worker document is created in MongoDB (or the existing one is returned) with Worker ID `W-XXXX`, name "Test Rider", zone "Noida Sector 18"
- A custom JWT token is issued and stored in the app's AuthContext
- You are redirected to the main tabbed application

**Evidence in backend logs:**
```
[AUTH] New worker registered: W-2102 (9999999999)
[AUTH] Firebase verified: 9999999999 → W-2102
```

### 3.4 Home Screen
After login, the Home screen shows:

**Hackathon Demo Info Banner:**
- A tappable blue card appears immediately below the greeting
- Text: "Hackathon Demo Info — Want to understand the app? Open the Guide section to learn how to evaluate features and trigger payouts."
- Tap it → Opens the full in-app Evaluation Guide (the `docs.tsx` screen)

**Greeting:**
- "Good morning / afternoon / evening, Test Rider" — dynamically based on time of day

**Zone Status Card:**
- Shows current zone: "Noida Sector 18"
- Shows "Inside Covered Zone" or "Outside Target Zone" based on GPS
- HyperTrack SDK runs in the background (mocked in Expo Go)

**Live Zone Conditions (real data from OpenWeatherMap + WAQI):**
- Rainfall in mm with safe/alert status (threshold: 50mm)
- Temperature in degrees C (threshold: 45°C)
- AQI reading (threshold: 400)

**Recent Payouts:**
- Shows the last 3 parametric payouts from MongoDB
- Each card shows: Disruption type, amount in Rs., date, status (paid/pending)

**Safety Shield Button:**
- A large "Activate Safety Mode" button at the bottom
- Tap it → Opens the Safety Mode flow for manual curfew/strike reporting

### 3.5 Real-Time Alert Banner (The Core Demo Moment)
This is the feature that makes the platform unique. Keep the Home screen open on your phone.

**Now trigger a disruption from the Admin Dashboard Simulation Studio.**

**What happens on your phone within 10 seconds:**
1. The alert polling interval (every 10 seconds) detects a new alert from `GET /worker/alerts?zoneId=Z001&since={timestamp}`
2. An animated banner slides down from the top of the screen showing:
   - A colored pill badge (FLOOD in cyan, RAIN in blue, HEAT in orange)
   - The alert message: "Flood alert detected in your zone"
   - The payout message: "Rs. 300 credited to your UPI"
   - The zone name: "Noida Sector 18"
3. The payout immediately appears in the Payouts tab history without any manual reload
4. The banner auto-dismisses after 8 seconds or can be manually dismissed with the X button

**This is the zero-touch parametric insurance working end-to-end in real time.**

### 3.6 Payouts Tab
Tap "Payouts" in the bottom navigation.

**What you will see:**
- A complete history of all parametric claims for the current worker
- Each payout shows: Disruption type, Rs. amount, date, status
- A total earnings summary at the top
- After triggering a disruption in the admin dashboard, the new payout appears here automatically within the next poll cycle

### 3.7 Earnings Tab
Tap "Earnings" in the bottom navigation.

**What you will see:**
- Weekly earnings summary
- Current No-Claim Streak counter (0 to 4 weeks)
- Progress bar showing how close the worker is to the 15% premium cashback
- Breakdown of premium paid vs. payouts received

### 3.8 Safety Tab
Tap "Safety" in the bottom navigation.

**What you will see:**
- A "Safety Mode" activation flow for reporting manual disruptions
- Workers can report: Curfew, Strike, Flooding, Road Blockage
- When activated, the GPS location is captured for fraud verification
- This deducts trust score if the claim is rejected after admin review
- A history of past Safety Mode activations is shown

### 3.9 Profile Tab
Tap "Profile" in the bottom navigation.

**What you will see:**
- Worker ID (e.g., W-2102)
- Phone number (9999999999)
- Zone assignment (Noida Sector 18)
- Current Trust Score (starts at 100)
- Active plan type
- UPI ID (masked)
- Logout button

---

## Part 4: Backend API — Direct Verification

All endpoints can be tested directly in a browser or using curl. The base URL is `https://ridershield-kxj0.onrender.com`.

### 4.1 Health Check
```
GET https://ridershield-kxj0.onrender.com/health
```
Expected response:
```json
{
  "status": "ok",
  "project": "RiderShield",
  "phase": 2,
  "db": "connected",
  "services": { "backend": "running", "mlService": "http://localhost:8000", "cronJob": "active" }
}
```

### 4.2 Weather Check for a City
```
POST https://ridershield-kxj0.onrender.com/weather/check
Content-Type: application/json

{ "city": "Noida" }
```
Expected response:
```json
{
  "city": "Noida",
  "rain_mm": 12,
  "temp": 34,
  "humidity": 68,
  "aqi_estimate": 187,
  "severity_score": 0.24,
  "alert": false
}
```

### 4.3 Worker Authentication (Demo Bypass)
```
POST https://ridershield-kxj0.onrender.com/auth/firebase-login
Content-Type: application/json

{
  "idToken": "DEMO_HACKATHON_TOKEN_123",
  "phone": "9999999999"
}
```
Expected response:
```json
{
  "token": "eyJhbGciOiJI...",
  "workerId": "W-2102",
  "name": "Test Rider",
  "zone": "Noida Sector 18",
  "isNewUser": false
}
```

### 4.4 Check Worker Alerts (Real-Time Polling Endpoint)
```
GET https://ridershield-kxj0.onrender.com/worker/alerts?zoneId=Z001&since=2026-01-01T00:00:00.000Z
```
Expected response after triggering a disruption:
```json
{
  "alerts": [
    {
      "id": "ALERT-1713360000000",
      "zoneId": "Z001",
      "zoneName": "Noida",
      "disruptionType": "flood",
      "severity": 0.85,
      "payoutAmount": 312,
      "status": "credited",
      "message": "Flood alert detected in your zone",
      "payoutMessage": "Rs. 312 credited to your UPI",
      "timestamp": "2026-04-17T12:00:00.000Z"
    }
  ]
}
```

### 4.5 Run Fraud Analysis Directly
```
POST https://ridershield-kxj0.onrender.com/fraud/analyze
Authorization: Bearer {admin_jwt_token}
Content-Type: application/json

{
  "zone": "Noida Sector 18",
  "disruptionType": "flood",
  "workerId": "W-2102",
  "trustScore": 85
}
```
Expected response:
```json
{
  "approved": true,
  "confidenceScore": 80,
  "fraudRiskLevel": "low",
  "passCount": 4,
  "checks": {
    "claimVelocity": { "pass": true, "reason": "Normal claim frequency in zone" },
    "trustScore": { "pass": true, "reason": "Trust score 85 is acceptable" },
    "timePattern": { "pass": true, "reason": "Claim within working hours" },
    "gpsCluster": { "pass": true, "reason": "Worker GPS confirmed in disrupted zone" },
    "deviceFingerprint": { "pass": true, "reason": "Device fingerprint matches registered device" }
  }
}
```

### 4.6 Get All Claims
```
GET https://ridershield-kxj0.onrender.com/claims
Authorization: Bearer {admin_jwt_token}
```
Returns all claims from MongoDB with full fraud metadata.

### 4.7 Get Worker Payout History
```
GET https://ridershield-kxj0.onrender.com/worker/payouts/W-2102
```
Returns all payouts for the demo worker.

### 4.8 Get Live Zone Status
```
GET https://ridershield-kxj0.onrender.com/zones
```
Returns all 3 covered zones with current risk scores, weather data, and active alert status.

---

## Part 5: ML Service — Direct Verification

Base URL: `https://ridershield-ml.onrender.com`

### 5.1 Health Check
```
GET https://ridershield-ml.onrender.com/health
```
Expected response: `{"status": "ok"}`

### 5.2 Predict Payout Amount (RandomForestRegressor)
```
POST https://ridershield-ml.onrender.com/predict-payout
Content-Type: application/json

{
  "earnings_baseline_hourly": 87.5,
  "disruption_type": 3,
  "severity_score": 0.85,
  "hours_affected": 4.0,
  "coverage_hours_per_day": 8.0,
  "max_weekly_payout": 600.0,
  "trust_score": 85.0,
  "zone_historical_impact": 0.72
}
```
Expected response:
```json
{ "payout_amount": 297.34 }
```
This output is generated by a trained RandomForestRegressor model. The exact amount will vary slightly based on model internals but will always be in the Rs. 200 to Rs. 600 range for these inputs.

### 5.3 Predict Weekly Premium (GradientBoostingRegressor)
```
POST https://ridershield-ml.onrender.com/predict-premium
Content-Type: application/json

{
  "zone_risk_score": 0.65,
  "earnings_baseline": 700.0,
  "rain_forecast_7d": 23.0,
  "heat_forecast_7d": 36.0,
  "aqi_forecast_7d": 187.0,
  "trust_score": 85.0,
  "historical_claim_rate": 0.12,
  "plan_type": 2,
  "worker_tenure_weeks": 14.0
}
```
Expected response:
```json
{ "weekly_premium": 89.43 }
```
This is dynamically calculated — not hardcoded. Different zone scores, earnings baselines, or risk forecasts will produce different premium amounts.

---

## Part 6: The Automated CRON Engine

This runs automatically on the deployed backend every 10 minutes. It does NOT require any manual action.

**What it does:**
Every 10 minutes the system:
1. Loops through all 3 registered zones (Noida, Delhi Rohini, Gurugram)
2. Fetches real-time weather data from OpenWeatherMap API
3. Fetches real-time AQI data from WAQI API
4. Queries NewsAPI for keywords: curfew, bandh, strike, Section 144, riot
5. If 2+ signals agree on a disruption crossing a threshold:
   - Runs the 5-signal fraud detection engine
   - If approved, calls the ML service to calculate the exact payout
   - Creates a MongoDB Claim document automatically
   - Adds the alert to the in-memory `activeAlerts` array
   - The Worker App picks this up on its next 10-second polling cycle

**To verify it is running:**
Call the health endpoint: `GET https://ridershield-kxj0.onrender.com/health`
The response includes `"cronJob": "active"` confirming it is scheduled.

To trigger it manually without waiting 10 minutes, use the "Trigger Detection Pulse" button in the Admin Dashboard Orchestration Hub.

---

## Part 7: Code Architecture Map

This maps every major feature claim to the exact code location for independent verification.

| Feature | File | Line |
|---|---|---|
| CRON multi-signal detection | `services/backend/index.js` | 800 |
| Signal 1: OpenWeatherMap real call | `services/backend/index.js` | 820 |
| Signal 2: WAQI AQI real call | `services/backend/index.js` | 847 |
| Signal 3: NewsAPI curfew scan | `services/backend/index.js` | 867 |
| Fraud Detection Engine | `services/backend/index.js` | 710 |
| Fraud: claim velocity check | `services/backend/index.js` | 719 |
| Fraud: trust score check | `services/backend/index.js` | 720 |
| Fraud: time pattern check | `services/backend/index.js` | 721 |
| Fraud: GPS cluster check | `services/backend/index.js` | 723 |
| Fraud: device fingerprint check | `services/backend/index.js` | 724 |
| Fraud check integrated in CRON | `services/backend/index.js` | 940 |
| Fraud analysis REST endpoint | `services/backend/index.js` | 1342 |
| Hackathon bypass (9999999999) | `services/backend/index.js` | 1640 |
| RazorpayX initialization | `services/backend/index.js` | 39 |
| RazorpayX payout dispatch | `services/backend/index.js` | 1838 |
| Firebase Admin SDK setup | `services/backend/index.js` | 18 |
| Worker alert polling endpoint | `services/backend/index.js` | 1700 |
| ML Payout Model endpoint | `services/ml-service/app.py` | 47 |
| ML Premium Model endpoint | `services/ml-service/app.py` | 66 |
| ML model training script | `services/ml-service/train.py` | 1 |
| Worker App real-time alert polling | `apps/worker-app/app/(tabs)/index.tsx` | 211 |
| Live payout injection (no reload) | `apps/worker-app/app/(tabs)/index.tsx` | 218 |
| Alert animated slide-down banner | `apps/worker-app/app/(tabs)/index.tsx` | 255 |
| Hackathon bypass UI (phone.tsx) | `apps/worker-app/app/onboarding/phone.tsx` | 98 |
| HyperTrack SDK service | `apps/worker-app/services/hypertrack.ts` | 1 |
| Zone GPS detection | `apps/worker-app/app/(tabs)/index.tsx` | 76 |

---

## Part 8: End-to-End Simulation Walkthrough

This is the recommended sequence for the complete "mic-drop" evaluation moment. Total time: under 5 minutes.

**Step 1 — Open the Worker App on your phone**
- Scan the QR from `npx expo start` using Expo Go
- Login with `9999999999` / `123456`
- Navigate to the Home tab and keep it open on your screen

**Step 2 — Open Admin Dashboard on your computer**
- Navigate to https://ridershieldadmin.vercel.app
- Login with `analyst@ridershield.in` / `analyst123`
- Go to Orchestration Hub → Simulation Studio

**Step 3 — Trigger the Flood**
- Zone: Noida Sector 18
- Type: Flood
- Severity: 0.85
- Click "Trigger Disruption"
- You will immediately see the ML-computed payout amount on the admin screen

**Step 4 — Watch your phone**
Within 10 seconds (the polling interval), the Worker App will:
1. Detect the new alert via the `/worker/alerts` polling endpoint
2. Trigger an animated banner sliding down from the top of the screen:
   - "FLOOD" badge in cyan
   - "Flood alert detected in your zone"
   - "Rs. 312 credited to your UPI"
3. The banner auto-dismisses after 8 seconds

**Step 5 — Verify in Payouts tab**
Tap the Payouts tab on the phone. The new Rs. 312 payout entry has been injected into the list without any manual refresh.

**Step 6 — Verify the claim in Admin Dashboard**
Go to Claims in the Admin Dashboard. The new claim is there with:
- Status: approved
- Fraud Risk: low
- Confidence Score: 80+
- Full 5-signal breakdown verifiable by clicking the claim

This is the complete, zero-touch, parametric insurance pipeline running end-to-end on real infrastructure.
