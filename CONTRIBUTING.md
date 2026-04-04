# Internal Team Guide: Developing RiderShield

Welcome to Phase 3! If you are reading this, you are part of the core RiderShield Hackathon team. 

This document outlines our internal git workflow so we don't accidentally overwrite each other's code as we integrate the final APIs (Setu, RazorpayX, etc.), plus a quick guide on how to boot up the massive mono-repo on your local machine.

---

## 🛠 Internal Git Workflow

Because we are working fast, merge conflicts can ruin our momentum. Please follow this internal rule strictly:

### 1. Claim What You're Working On
Before you start coding, check our GitHub **Issues Tab** or our team group chat. 
* If no one is working on the feature, **create a new Issue** on the GitHub repository.
* Title it clearly (e.g., "Add RazorpayX to Backend" or "Fix Vercel CORS bug"). This tells the rest of the team: *"I own this right now, don't touch it."*

### 2. Do Not Push to Main directly
We are using feature branches to keep the `main` branch clean and always deployable.

1. **Clone the repository** (if you haven't already):
   ```bash
   git clone https://github.com/aadiexii/RIDERSHIELD.git
   cd RIDERSHIELD
   ```
2. **Create a fresh branch** for your specific task:
   ```bash
   git checkout -b feature/razorpay-integration
   # or
   git checkout -b fix/auth-bug
   ```

### 3. Commit and Pull Request (PR)
When your feature is working locally:
```bash
git add .
git commit -m "feat: added RazorpayX API integration"
git push origin feature/razorpay-integration
```
After pushing, jump into GitHub and raise a **Pull Request** against `main`. Tag another teammate to quickly review it before we merge.

---

## 💻 Local Installation Guide

RiderShield is a mono-repo containing 4 distinct services. You will need **Node.js (18+)** and **Python (3.9+)** installed on your machine to run it locally.

### 1. Start the Backend (`services/backend`)
The backend is the central router of the application.
```bash
cd services/backend
npm install

# Ask Shivam for the .env details (MONGODB_URI, JWT_SECRET, WAQI_API_KEY)

node index.js
# Runs on http://localhost:5000
```

### 2. Start the Machine Learning Engine (`services/ml-service`)
This Python FastAPI service manages the actuarial premium and payout mathematics.
```bash
cd services/ml-service
pip install -r requirements.txt

# Run the training script first to generate the local .pkl models
python train.py

# Start the FastAPI server
uvicorn app:app --host 0.0.0.0 --port 8000
# Runs on http://localhost:8000
```

### 3. Start the Web Dashboard (`apps/admin-dashboard`)
The React (Vite) admin portal for monitoring claims and simulating disruptions.
```bash
cd apps/admin-dashboard
npm install

# Make sure you have a .env file linking VITE_API_URL to http://localhost:5000

npm run dev
# Runs on http://localhost:5173
```

### 4. Start the Mobile Worker App (`apps/worker-app`)
The React Native (Expo) app used by the delivery partners on the ground.
```bash
cd apps/worker-app
npm install

# Ask the team for the EXPO_PUBLIC_API_URL before starting
npx expo start
```
*Note: You can use the Expo Go app on your physical phone to scan the QR code and test the app live!*

---

Let's crush Phase 3 and win this!
