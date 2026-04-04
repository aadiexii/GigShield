# RiderShield Phase 2 Complete: Beyond Basic Parametric Insurance

Welcome to Phase 2 of RiderShield. We are aware that many hacks address gig worker insurance by simply hooking an OpenWeather API to a payout script. RiderShield is different. 

While our core concept uses parametric data, we spent Phase 2 building the **opinionated, edge-case infrastructure** required to deploy this in the real world—specifically in India. A weather API cannot detect a sudden local strike, it cannot prove a worker's historical income, and it cannot stop coordinated GPS-spoofing fraud rings. **We built systems that can.**

You can read this document to fully understand our unique backend architecture, our live ML pipelines, and our crowdsourced safety systems. 

---

## 🚦 Evaluating This Project (Live Links & Walkthrough)

We strongly encourage you to interact with the live deployed ecosystem. Here is everything you need to test the end-to-end flow:

### 1. The Web Dashboard (Admin Control Center)
**🔗 Live Link:** [https://ridershield.vercel.app](https://ridershield.vercel.app)

*   **The Landing Page:** Scroll through the public marketing site to understand our thesis on gig-worker protection.
*   **Admin Login:** Click "Admin Login" in the top right.
    *   *Note on Auth:* Full Google Single Sign-On (SSO) architecture is built-in, but to make evaluation frictionless, we have provided **Demo Credentials** directly on the login screen.
    *   Simply click the `SuperAdmin` demo credential box at the bottom, and it will auto-fill the email and password. Click "Sign In".
*   **The Dashboard Walkthrough:**
    *   In the Admin portal, notice the **Live Analytics**. 
    *   Scroll down to the **Simulate Disruption** panel. Select "Zone A - Central Ops" and choose a disruption type (like "Riot" or "Heavy Rain"). 
    *   Click **Trigger Emergency Protocol**. 
    *   *What actually happens under the hood:* Your Vercel frontend just sent a payload to our Node.js Backend. The Backend validated it, and instantly pinged our Python ML Engine to run the **Fraud Analysis Algorithm** and calculate a dynamic payout fraction. Look at the resulting popup—that is live ML data coming back!

### 2. The Machine Learning Engine (FastAPI)
**🔗 Live Link:** [https://ridershield-ml.onrender.com/health](https://ridershield-ml.onrender.com/health)

Clicking the link above will return `{"status":"ok","service":"ML Prediction Engine"}`. This proves our Python backend is actively running 2-layer regression models (Gradient Boosting for Premiums, Random Forest for Payouts) 24/7 on the cloud.

### 3. The Node.js Backend API
**🔗 Live Link:** [https://ridershield-kxj0.onrender.com/health](https://ridershield-kxj0.onrender.com/health)

This Node server manages our MongoDB connections, serves requests to the frontend, and runs the 15-minute scheduled Cron Jobs to check OpenWeather API and the World Air Quality Index (WAQI) for autonomous event detection.

### 4. The Mobile Worker App (Android APK)
Attached to our submission package is the compiled `.apk` file for the Delivery Partner Application. 
*   Install it on any Android device.
*   It showcases the exact UI the delivery driver sees.
*   It includes the vital UX for our **"Safety Mode"** crowdsourced mesh network.

---

## ⚡ Our "Unexpected" Engineering Differentiators

This is not a boilerplate application. We purpose-built three massive differentiators to solve the actual problems of gig-worker insurance at scale:

### 1. "Safety Mode" — A Crowdsourced Mesh Network
APIs do not cover everything. If a sudden local bandh (strike) or a severe road blockade occurs in Gurugram, no weather API will trigger a payout, but workers still lose income. 
We built **Safety Mode**: a physical button in the worker app. If one worker presses it, nothing happens. But if **3 workers in the exact same zone press it within 30 minutes**, our backend registers a "Group Signal". It immediately cross-references local city data via NewsAPI for keywords like "curfew" or "protest". If corroborated, it overrides the standard system and authorizes emergency payouts. **We built a human-centric disruption tracker for unmapped events.**

### 2. Setu Account Aggregator Integration (Income Baseline)
Most parametric platforms guess a flat payout amount. We engineered our backend to integrate with the RBI's **Account Aggregator framework**. Instead of taking Zomato's word for it, RiderShield will securely read the worker's official bank statement, scan for "ZOMATO PVT LTD" or "BUNDL TECHNOLOGIES" credits, and mathematically establish a highly accurate "Earnings Baseline". Our Python ML engine then uses this exact baseline to calculate personalized premiums. 

### 3. A 5-Layer ML Fraud Detection Engine
You cannot offer automatic payouts without enterprise-grade security. Our backend intercepts every single disruption trigger (even automated weather ones) and runs a rigorous 5-layer fraud check before authorizing a Razorpay UPI transfer:
1. **Claim Velocity**: Is this zone suddenly generating 50 claims in 5 minutes? (Flags coordinated rings).
2. **GPS Cluster Analysis**: Do the GPS coordinates match the disrupted zone?
3. **Time Profiling**: Did the disruption happen during the worker's usual delivery hours?
4. **Device Fingerprinting**: Is this claim coming from a known, non-emulated device?
5. **Trust Score Gate**: Does the worker have a high enough behavioral historical score?

*Only if the engine returns a High Confidence Score does the payout execute automatically. Otherwise, it is diverted to the Admin Dashboard for manual review.*

---

## ⚠️ Our Intentional Mock Data Strategy

During a hackathon evaluation, we want judges to immediately experience the platform. We did not want you to have to wait for it to actually flood in Delhi just to see our UI respond.

**What is Real code:** 
The Machine Learning math, the Node.js routing, the UI state management, the 15-minute Cron architecture, the 5-layer Fraud Engine algorithms, the JWT Auth logic, and the cloud infrastructure.

**What is Seeded (Mocked) data:**
Every worker name in the database, the historical claims ledger, the active fraud alerts on the dashboard, and the mobile validation screens (Aadhaar/OTP are bypassed). 

This purposeful design choice allows you to click through the applications and watch the ML premium calculator update dynamically in real-time without the friction of a blank database.

---

## 🚀 Moving Towards Phase 3

Phase 2 successfully proved our custom logic works end-to-end. As we move into Phase 3, we are not changing the architecture; we are simply swapping our mocked data layers with live vendor SDKs:
1. Connecting the **Setu Account Aggregator API** to fetch live banking transaction baselines.
2. Integrating the **RazorpayX API** to convert our simulated success notifications into real, finalized UPI deposits.
3. Injecting the **HyperTrack SDK** into the React Native app for real-time, untamperable GPS verification leading up to a claim.
