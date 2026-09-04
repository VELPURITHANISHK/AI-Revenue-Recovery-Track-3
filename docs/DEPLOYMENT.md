# Deployment Guide

This guide covers deploying the AI Revenue Recovery Agent and the mocked E-commerce storefront to production environments (e.g., Render, Vercel, Heroku, or AWS).

## Requirements
- **MongoDB Atlas** (Shared database)
- **Redis Provider** (e.g., Upstash, Redis Labs, or Render Redis)
- **Razorpay Account** (Test Mode credentials)
- **OpenAI Account** (API Key)
- **Email Provider** (e.g., Resend, SendGrid)

---

## 1. Environment Setup

### A. MongoDB Atlas
1. Create a cluster on MongoDB Atlas.
2. Allow access from anywhere (`0.0.0.0/0`) or configure VPC peering.
3. Obtain the connection string: `mongodb+srv://<user>:<password>@cluster.mongodb.net/ecommerce_db`

### B. Redis
1. Create a Redis instance (must support BullMQ requirements).
2. Obtain the Redis URL: `rediss://default:<password>@<host>:<port>`

---

## 2. Deploying E-commerce Application

### C. E-commerce Backend (Node.js/Express)
1. Set the build directory to `ecommerce/server`.
2. **Start Command:** `npm start`
3. **Environment Variables:**
   ```env
   PORT=5000
   MONGODB_URI=mongodb+srv://...
   RAZORPAY_KEY_ID=rzp_test_...
   RAZORPAY_KEY_SECRET=...
   RAZORPAY_WEBHOOK_SECRET=your_secure_webhook_secret
   FRONTEND_URL=https://your-ecommerce-frontend.com
   ```
4. Verify deployment by visiting `https://your-ecommerce-backend.com/api/health`.

### D. E-commerce Frontend (React/Vite)
1. Set the build directory to `ecommerce/client`.
2. **Build Command:** `npm run build`
3. **Environment Variables:**
   ```env
   VITE_API_URL=https://your-ecommerce-backend.com/api
   ```

---

## 3. Deploying AI Recovery Application

### E. Recovery Backend (Node.js/Express)
1. Set the build directory to `ai-revenue-recovery/server`.
2. **Start Command:** `npm start`
3. **Environment Variables:**
   ```env
   PORT=5001
   MONGODB_URI=mongodb+srv://...
   OPENAI_API_KEY=sk-...
   REDIS_URL=rediss://...
   RAZORPAY_KEY_ID=rzp_test_...
   RAZORPAY_KEY_SECRET=...
   EMAIL_API_KEY=...
   FRONTEND_URL=https://your-recovery-dashboard.com
   ECOMMERCE_API_URL=https://your-ecommerce-backend.com/api
   DEMO_MODE=true
   ```
4. Verify deployment by visiting `https://your-recovery-backend.com/api/health`.

### F. Recovery Frontend (React/Vite)
1. Set the build directory to `ai-revenue-recovery/client`.
2. **Build Command:** `npm run build`
3. **Environment Variables:**
   ```env
   VITE_API_URL=https://your-recovery-backend.com
   ```

---

## 4. Deploying the Background Worker

### G. BullMQ Recovery Worker
The worker *must* run as a separate background process or service to ensure scalable and reliable job processing.
1. Deploy a new background worker service pointing to `ai-revenue-recovery/server`.
2. **Start Command:** `npm run worker`
3. **Environment Variables:**
   ```env
   MONGODB_URI=mongodb+srv://...
   REDIS_URL=rediss://...
   OPENAI_API_KEY=sk-...
   RAZORPAY_KEY_ID=rzp_test_...
   RAZORPAY_KEY_SECRET=...
   EMAIL_API_KEY=...
   DEMO_MODE=true
   ```

---

## 5. Razorpay Webhook Configuration

1. Go to the Razorpay Dashboard -> Webhooks.
2. Add a new webhook pointing to your E-commerce Backend:
   `https://your-ecommerce-backend.com/api/webhooks/razorpay`
3. **Secret:** Ensure this matches `RAZORPAY_WEBHOOK_SECRET` from Step 2C.
4. **Events to subscribe to:**
   - `payment.failed`
   - `payment.captured`
   - `order.paid`

---

## 6. CORS Configuration
Ensure that both backend services have their `FRONTEND_URL` environment variables set accurately to match the deployed frontends. This prevents cross-origin resource sharing errors when the dashboards try to fetch data.
