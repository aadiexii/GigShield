# Contributing to RiderShield

First off, thank you for considering contributing to RiderShield! It's people like you that make RiderShield such a great platform for the gig economy. 

This document outlines the process for contributing to the repository and provides a comprehensive guide on how to set up the project locally.

---

## 🛠 Contribution Workflow

To avoid duplicated efforts and merge conflicts, please strictly follow this workflow:

### 1. Create an Issue First
**Before you write any code**, please check the [Issues tab](../../issues) to see if someone is already working on the feature or bug you want to tackle.
* If no one is working on it, **create a new Issue** describing what you plan to do.
* Assign yourself to the issue (or leave a comment saying "I am working on this") so the rest of the team knows it is claimed.

### 2. Fork and Clone
We use a Fork & Pull Request model. Please do not push directly to the `main` branch of this repository.

1. **Fork** this repository to your own GitHub account.
2. **Clone** your fork locally:
   ```bash
   git clone https://github.com/YOUR-USERNAME/GigShield.git
   cd GigShield
   ```
3. Add the original repository as an upstream remote:
   ```bash
   git remote add upstream https://github.com/aadiexii/RIDERSHIELD.git
   ```

### 3. Create a Branch
Create a new branch for your feature or bugfix. Name it descriptively:
```bash
git checkout -b feature/your-feature-name
# or
git checkout -b fix/your-bugfix-name
```

### 4. Commit and Push
Make your changes, commit them with descriptive messages, and push to *your fork*:
```bash
git add .
git commit -m "feat: added new awesome feature"
git push origin feature/your-feature-name
```

### 5. Create a Pull Request (PR)
Go to the original RiderShield repository and click **New Pull Request**. Select your branch. In the PR description, mention the issue you are fixing (e.g., `Fixes #12`).

---

## 💻 Local Installation Guide

RiderShield is a mono-repo containing 4 distinct services. You will need **Node.js (18+)** and **Python (3.9+)** installed on your machine.

### 1. Connect the Backend (`services/backend`)
The backend is the central router of the application.
```bash
cd services/backend
npm install

# Create a .env file based on .env.example (or ask the team for development keys)
# You will need your local MONGODB_URI and JWT_SECRET

node index.js
# The backend will start on http://localhost:5000
```

### 2. Connect the Machine Learning Engine (`services/ml-service`)
This Python FastAPI service manages the actuarial premium and payout calculations.
```bash
cd services/ml-service
pip install -r requirements.txt

# Run the training script first to generate the local .pkl model files
python train.py

# Start the FastAPI server
uvicorn app:app --host 0.0.0.0 --port 8000
# The ML engine will start on http://localhost:8000
```

### 3. Start the Web Dashboard (`apps/admin-dashboard`)
The React (Vite) admin portal for monitoring claims and simulating disruptions.
```bash
cd apps/admin-dashboard
npm install

# Start the Vite development server
npm run dev
# The website will start on http://localhost:5173
```
*Note: Make sure your `VITE_API_URL` environment variable points to `http://localhost:5000` (the backend).*

### 4. Start the Mobile Worker App (`apps/worker-app`)
The React Native (Expo) app used by the delivery partners on the ground.
```bash
cd apps/worker-app
npm install

# Start the Expo bundler
npx expo start
```
*Note: You can use the Expo Go app on your physical phone to scan the QR code and test the app live!*

---

## 🤝 Code of Conduct
Please be respectful to other contributors. We are building a platform to protect workers, and that spirit of protection and respect extends to our development team. Happy coding!
