# RiderShield

Welcome to the **RiderShield** repository. 

RiderShield is a zero-touch, parametric income-protection platform for gig workers in India. It automatically triggers payouts when external disruptions like extreme weather, severe pollution smog, city-wide curfews, or strikes prevent delivery partners from working. 

## Pitch Deck

[View the RiderShield Pitch Deck](https://docs.google.com/presentation/d/1Ew7mLHs0yndL273A_NAtGwg8omcztNpr/edit?usp=sharing&ouid=102366805236840213099&rtpof=true&sd=true)

## Live Deployments

| Service | URL | Status |
|---|---|---|
| Public Website | [ridershieldpublic.vercel.app](https://ridershieldpublic.vercel.app) | Live |
| Admin Dashboard | [ridershieldadmin.vercel.app](https://ridershieldadmin.vercel.app) | Live |
| Backend API | [ridershield-kxj0.onrender.com](https://ridershield-kxj0.onrender.com) | Live |
| ML Service | [ridershield-ml.onrender.com](https://ridershield-ml.onrender.com) | Live |
| Worker App (Android APK) | Build in progress — link will be updated here shortly | Building |

> The backend and ML service are hosted on Render's free tier. They may take up to 50 seconds to respond after a period of inactivity.

## Demo Credentials

| Role | Credential | Value |
|---|---|---|
| Worker App | Phone | `9999999999` |
| Worker App | OTP | `123456` |
| Admin Dashboard | Email | `analyst@ridershield.in` |
| Admin Dashboard | Password | `analyst123` |

> For the complete feature-by-feature evaluation guide including all API endpoints, E2E simulation steps, and code location map, see [README(Phase3).md](./README(Phase3).md).


To help judges and contributors understand the evolution and architecture of our product, we have split our documentation into three distinct phases.

---

### [Phase 1: Research, Validation and Architecture](./README(Phase1).md)
Phase 1 covers the fundamental problem we are solving, our initial technical architecture, competitive analysis, and how we validated the concept of parametric insurance for the gig economy.

### [Phase 2: Live Deployment and Machine Learning](./README(Phase2).md)
Phase 2 covers what we actually built and deployed. It details the Python Machine Learning engines (Fraud detection, Premium calculation), the Node.js disruption detection cron jobs, our crowdsourced 'Safety Mode' mesh network, and the full-stack architecture running live.

### [Phase 3: Final Integrations and E2E Demo](./README(Phase3).md)
Phase 3 documents our final technical integrations for the hackathon. This includes our mock RazorpayX implementation, Expo Push Notifications, Firebase Authentication bypasses, the Public Website addition, and the integrated Judge Demo evaluation workflows.

---

*Built for gig workers across India.*
