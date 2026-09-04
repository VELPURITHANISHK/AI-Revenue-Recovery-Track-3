# 🤖 AI Revenue Recovery Agent

> **Don't just detect failed payments. Recover the revenue.**

An AI-powered revenue recovery system built for the **Razorpay Buildathon — Track 03: AI Revenue Recovery**.

This repository contains two applications:

- 🛒 **E-commerce Application** — creates orders and Razorpay test payments
- 🤖 **AI Revenue Recovery Agent** — detects failed payments, decides recovery actions, automates follow-ups, and measures recovered revenue

---

# 🌐 LIVE DEMO

## 🛒 E-commerce Application

**Project Folder:**

```text
/ecommerce
```

**Live Website:**

https://ecommerce-eight-psi-85.vercel.app/

👉 **Launch E-commerce:**  
https://ecommerce-eight-psi-85.vercel.app/

---

## 🤖 AI Revenue Recovery Agent

**Project Folder:**

```text
/ai-revenue-recovery
```

**Live Dashboard:**

https://ai-revenue-recovery-eight.vercel.app/

👉 **Launch AI Revenue Recovery:**  
https://ai-revenue-recovery-eight.vercel.app/

### 👤 Human Escalations

https://ai-revenue-recovery-eight.vercel.app/escalations

👉 **Open Escalations:**  
https://ai-revenue-recovery-eight.vercel.app/escalations

---

# 🎯 What Is This Project?

This project is an **AI Agent for Revenue Recovery**.

When a customer's payment fails, the system does not simply show:

```text
Payment Failed ❌
```

Instead, it starts an automated recovery workflow:

```text
Payment Failed
      ↓
Detect Revenue at Risk
      ↓
Understand Customer + Payment Context
      ↓
AI Agent Chooses Recovery Strategy
      ↓
Backend Validates Decision
      ↓
Controlled Tool Executes Action
      ↓
BullMQ + Redis Automates Follow-up
      ↓
Payment Status Checked
      ↓
┌──────────────┬──────────────┐
↓              ↓              ↓
Recovered 💰  Escalated 👤  Stopped 🛑
```

The goal is simple:

> **Turn failed payments into recovered revenue.**

---

# 💡 The Problem

Revenue can be lost when:

- A payment fails
- A customer abandons checkout
- A payment repeatedly fails
- A customer needs another payment attempt
- A high-value customer requires human intervention

Traditional systems often stop after identifying the failure.

Our system starts the recovery process.

---

# 🚀 Our Solution

The AI Revenue Recovery Agent:

1. Detects failed payments
2. Understands payment and customer context
3. Classifies the failure
4. Determines the risk level
5. Uses AI to select a recovery strategy
6. Validates the AI decision using backend policies
7. Executes a controlled recovery tool
8. Schedules automated follow-ups
9. Checks the latest payment status
10. Stops when payment succeeds
11. Escalates difficult cases to a human
12. Measures actual recovered revenue

---

# 🧠 Why Is This an AI Agent?

This is **not just an AI chatbot**.

The AI participates in an operational workflow.

```text
Customer / Payment Data
          ↓
     Context Builder
          ↓
       AI Agent
          ↓
   Recovery Decision
          ↓
 Backend Policy Layer
          ↓
    Tool Executor
          ↓
   Real-world Action
          ↓
     Automation
          ↓
 Payment Verification
          ↓
      Final Outcome
```

The AI can select from controlled actions such as:

```text
SEND_REMINDER
CREATE_PAYMENT_LINK
RETRY_PAYMENT
ESCALATE_TO_HUMAN
STOP_RECOVERY
```

The backend, not the AI, controls the actual execution.

---

# 🛡️ AI Safety & Guardrails

The AI does not have unrestricted access to the system.

The AI cannot:

- Directly access MongoDB
- Directly access Redis
- Access API secrets
- Execute arbitrary code
- Call unknown backend functions
- Mark a payment as successful

Instead:

```text
AI Decision
     ↓
Backend Validation
     ↓
Safety Policy
     ↓
Allowed Tool
     ↓
Tool Execution
```

The system also has:

- Maximum retry limits
- Maximum reminder limits
- Maximum recovery attempts
- Duplicate-action protection
- Idempotency
- Payment-state verification
- Recovery stopping rules
- Human escalation
- Server-side secrets

---

# 🏗️ Project Structure

Both applications are stored in the same GitHub repository.

```text
AI-Revenue-Recovery/
│
├── ecommerce/
│   │
│   ├── client/                  # E-commerce React frontend
│   ├── server/                  # E-commerce Node/Express backend
│   ├── package.json
│   └── ...
│
├── ai-revenue-recovery/
│   │
│   ├── client/                  # Recovery React dashboard
│   ├── server/                  # Recovery Node/Express backend
│   ├── package.json
│   └── ...
│
├── README.md
├── .gitignore
└── .env.example
```

### Application Paths

```text
/ecommerce
```

is responsible for the customer shopping and payment experience.

```text
/ai-revenue-recovery
```

is responsible for AI-powered payment recovery.

Both applications use the **same MongoDB database**.

---

# 🛒 1. E-commerce Application

The e-commerce application provides the payment environment used by the recovery system.

### Features

- Product listing
- Product details
- Shopping cart
- Checkout
- Orders
- Razorpay Test Mode
- Payment verification
- Payment failure handling
- Razorpay webhooks
- MongoDB persistence

### Payment Flow

```text
Customer
   ↓
Product
   ↓
Cart
   ↓
Checkout
   ↓
Razorpay
   ↓
Payment
   ↓
Server-side Verification
   ↓
MongoDB
```

A failed payment becomes a potential recovery case.

---

# 🤖 2. AI Revenue Recovery Application

The recovery application monitors failed payments and manages the recovery workflow.

### Features

- Failed payment detection
- Customer context
- Payment context
- Customer segmentation
- Failure classification
- Risk assessment
- AI decision engine
- AI tool calling
- Payment link creation
- Reminder sending
- Recovery automation
- BullMQ
- Redis
- Background worker
- Human escalation
- Recovery timeline
- Safety limits
- Duplicate protection
- Revenue metrics
- Demo mode

---

# 🔄 Complete Recovery Workflow

## 1. Detect

A payment fails.

```text
Payment Status = FAILED
```

The system identifies the payment as revenue at risk.

---

## 2. Understand

The system gathers context such as:

- Customer
- Order
- Payment amount
- Currency
- Failure reason
- Previous successful payments
- Previous failed payments
- Previous recovery attempts
- Customer segment
- Risk level

---

## 3. Decide

The AI analyzes the available context.

Example:

```json
{
  "customerSegment": "LOYAL_CUSTOMER",
  "failureCategory": "INSUFFICIENT_FUNDS",
  "riskLevel": "LOW",
  "strategy": "GENTLE_REMINDER",
  "decision": "SEND_REMINDER",
  "confidence": 0.91,
  "reason": "First payment failure with strong successful payment history.",
  "recommendedDelaySeconds": 10,
  "nextAction": "CHECK_PAYMENT"
}
```

The application displays concise decision reasoning.

Private chain-of-thought is not exposed.

---

# 4. Validate

The backend validates the AI decision.

It checks:

- Is the payment valid?
- Has the payment already succeeded?
- Is the requested action allowed?
- Has the recovery limit been reached?
- Is this action duplicated?
- Does the decision follow the recovery policy?

---

# 5. Act

The backend can execute controlled tools.

```text
sendReminder()
createPaymentLink()
retryPayment()
escalateToHuman()
stopRecovery()
```

The AI requests an action.

The backend decides whether that action is allowed.

---

# 6. Automate

BullMQ and Redis manage delayed recovery jobs.

Example:

```text
Payment Failed
      ↓
AI Decision
      ↓
Send Reminder
      ↓
Wait
      ↓
Check Payment
      ↓
Still Unpaid?
      ↓
Next Recovery Action
```

The recovery worker executes scheduled jobs independently of the frontend.

---

# 7. Verify

Before performing an automated action, the worker checks the latest payment state.

If the customer has already paid:

```text
Payment Captured
       ↓
Recovery Case = RECOVERED
       ↓
Cancel Pending Jobs
       ↓
Stop Further Recovery
```

This prevents unnecessary reminders after successful payment.

---

# 💳 Razorpay Integration

The e-commerce application uses **Razorpay Test Mode**.

The system supports payment events including:

```text
payment.captured
payment.failed
order.paid
```

Payment verification happens on the server.

The frontend is never trusted to declare payment success.

> ⚠️ This project uses Razorpay Test Mode for development and demonstration. No real money is processed.

---

# 📊 Revenue Recovery Dashboard

The AI Revenue Recovery dashboard provides visibility into recovery performance.

### Main Metrics

```text
Revenue at Risk
Recovered Revenue
Recovery Rate
Active Recovery Cases
Successful Recoveries
Human Escalations
Average Recovery Time
```

Example:

```text
┌─────────────────────────────────┐
│       RECOVERY PERFORMANCE      │
├─────────────────────────────────┤
│ Revenue at Risk       ₹52,400   │
│ Recovered Revenue     ₹31,200   │
│ Recovery Rate           59.5%   │
│ Active Cases               6    │
│ Successful Recoveries       11  │
│ Human Escalations            3  │
└─────────────────────────────────┘
```

> The values above are illustrative examples only.

---

# 💰 How Is Recovered Revenue Calculated?

Recovered revenue is **not** calculated from:

```text
AI confidence
Reminder sent
Payment link created
Number of recovery attempts
AI prediction
```

It is counted only after the payment is actually confirmed as successful.

```text
Failed Payment
      ↓
Recovery Action
      ↓
Customer Pays
      ↓
Payment Confirmed
      ↓
Recovered Revenue
```

---

# 🧾 Audit Trail

Every recovery case can maintain a timeline of actions.

Example:

```text
Payment Failed
      ↓
AI Analysis Completed
      ↓
Strategy Selected
      ↓
Backend Validation Passed
      ↓
Recovery Tool Executed
      ↓
Follow-up Scheduled
      ↓
Payment Status Checked
      ↓
Payment Captured
      ↓
Recovery Completed
```

This provides visibility into what happened during the recovery process.

---

# 👤 Human Escalation

Not every payment should be recovered automatically.

High-risk or repeatedly failed payments can be escalated.

Example:

```text
High-value payment
        ↓
Repeated failures
        ↓
High risk
        ↓
AI recommends escalation
        ↓
Backend validates
        ↓
Human escalation
```

View escalated cases:

https://ai-revenue-recovery-eight.vercel.app/escalations

---

# 🔁 Example Recovery Scenarios

## Scenario 1 — First Failed Payment

```text
Payment Failed
      ↓
Customer History
      ↓
Low Risk
      ↓
Gentle Reminder
      ↓
Follow-up
      ↓
Payment Success
      ↓
RECOVERED 💰
```

---

## Scenario 2 — High-Value / Repeated Failure

```text
Payment Failed
      ↓
Customer + Payment History
      ↓
High Risk
      ↓
Payment Link / Human Escalation
      ↓
Human Intervention
```

---

## Scenario 3 — Customer Pays Before Recovery

```text
Payment Failed
      ↓
Recovery Job Scheduled
      ↓
Customer Pays
      ↓
Worker Checks Latest Status
      ↓
Payment Captured
      ↓
Cancel Recovery Jobs
      ↓
RECOVERED 💰
```

No additional recovery message should be sent after payment success.

---

# ⚙️ Technology Stack

## E-commerce

- React
- Vite
- Node.js
- Express.js
- MongoDB
- Mongoose
- Razorpay

## AI Revenue Recovery

- React
- Vite
- Node.js
- Express.js
- MongoDB
- Mongoose
- OpenAI API
- BullMQ
- Redis

## Architecture

- REST APIs
- Webhooks
- Background workers
- Queue-based automation
- AI tool calling
- Server-side validation

---

# 💻 Run Locally

## Prerequisites

Install:

- Node.js
- npm
- MongoDB / MongoDB Atlas
- Redis
- Razorpay account
- OpenAI API key
- Email provider/API if required

Check Node.js:

```bash
node -v
```

Check npm:

```bash
npm -v
```

---

# 1️⃣ Clone the Repository

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd <YOUR_PROJECT_FOLDER>
```

---

# 2️⃣ Configure Environment Variables

Create the required `.env` files based on the `.env.example` files.

Typical variables include:

```env
MONGODB_URI=

RAZORPAY_KEY_ID=
RAZORPAY_KEY_SECRET=
RAZORPAY_WEBHOOK_SECRET=

OPENAI_API_KEY=

REDIS_URL=

EMAIL_API_KEY=

FRONTEND_URL=

DEMO_MODE=true
```

> Use the exact variable names from the `.env.example` files in the project.

Never commit real API keys or secrets to GitHub.

---

# 3️⃣ Start MongoDB

You can use MongoDB Atlas or a local MongoDB installation.

For MongoDB Atlas:

```env
MONGODB_URI=your_mongodb_connection_string
```

Both applications should point to the same database.

```text
E-commerce
     ↓
 MongoDB
     ↑
Recovery Agent
```

---

# 4️⃣ Start Redis

For local Redis:

```env
REDIS_URL=redis://localhost:6379
```

Or use a cloud Redis provider.

Redis is used by BullMQ for recovery job scheduling and processing.

---

# 5️⃣ Run E-commerce

Open a terminal:

```bash
cd ecommerce
npm install
```

Then run the application using the scripts defined in its `package.json`.

For example:

```bash
npm run dev
```

If frontend and backend are separate applications, start them from their respective directories.

---

# 6️⃣ Run AI Revenue Recovery

Open another terminal:

```bash
cd ai-revenue-recovery
npm install
```

Start the recovery application using the scripts defined in its `package.json`.

For example:

```bash
npm run dev
```

---

# 7️⃣ Run the Recovery Worker

Open another terminal:

```bash
cd ai-revenue-recovery
npm run worker
```

The worker connects to:

```text
MongoDB
   +
Redis
```

and processes recovery jobs created by BullMQ.

---

# 🧪 Demo Mode

For hackathon demonstrations:

```env
DEMO_MODE=true
```

Demo mode uses short delays so the complete recovery process can be demonstrated quickly.

Example:

```text
Payment Failed
      ↓
AI Decision
      ↓
Reminder
      ↓
Short Delay
      ↓
Payment Check
      ↓
Recovery
```

Demo mode does **not** fake payment success.

A payment is marked recovered only after the payment state is actually confirmed.

---

# 🎬 How to Run the Complete Demo

### Step 1

Open the e-commerce application:

https://ecommerce-eight-psi-85.vercel.app/

### Step 2

Choose a product.

### Step 3

Add the product to the cart.

### Step 4

Proceed to checkout.

### Step 5

Use Razorpay Test Mode.

### Step 6

Create a failed payment scenario.

### Step 7

Open the AI Revenue Recovery dashboard:

https://ai-revenue-recovery-eight.vercel.app/

### Step 8

Find the failed payment.

### Step 9

Run AI Recovery.

### Step 10

Show:

```text
Customer Segment
Failure Category
Risk Level
Strategy
Decision
Confidence
Reason
Next Action
```

### Step 11

Show the recovery tool being executed.

### Step 12

Show BullMQ scheduling the next recovery action.

### Step 13

Complete the payment using Razorpay Test Mode.

### Step 14

Show:

```text
Payment Captured
      ↓
Recovery Completed
      ↓
Recovered Revenue Updated
```

### Step 15

Show the dashboard metrics.

---

# 🧱 System Architecture

```text
                         CUSTOMER
                            │
                            ↓
                   ┌─────────────────┐
                   │  E-COMMERCE UI  │
                   │ React + Vite    │
                   └────────┬────────┘
                            ↓
                   ┌─────────────────┐
                   │ E-COMMERCE API  │
                   │ Node + Express  │
                   └────────┬────────┘
                            ↓
                        RAZORPAY
                            │
                            ↓
                         MONGODB
                            │
                     Payment Failed
                            │
                            ↓
                   ┌─────────────────┐
                   │  RECOVERY API   │
                   │ Node + Express  │
                   └────────┬────────┘
                            ↓
                   ┌─────────────────┐
                   │    AI AGENT     │
                   │     OpenAI      │
                   └────────┬────────┘
                            ↓
                   ┌─────────────────┐
                   │ POLICY / SAFETY │
                   └────────┬────────┘
                            ↓
                   ┌─────────────────┐
                   │ TOOL EXECUTOR   │
                   └────────┬────────┘
                            ↓
                   ┌─────────────────┐
                   │  BULLMQ + REDIS │
                   └────────┬────────┘
                            ↓
                   ┌─────────────────┐
                   │ RECOVERY WORKER │
                   └────────┬────────┘
                            ↓
              ┌─────────────┼─────────────┐
              ↓             ↓             ↓
          Reminder     Payment Link   Escalation
              │             │
              └─────────────┘
                            ↓
                    PAYMENT CHECK
                            ↓
              RECOVERED / STOPPED / ESCALATED
```

---

# 🔐 Security

Important security principles:

- Secrets are stored in environment variables
- OpenAI API keys remain server-side
- Razorpay secrets remain server-side
- MongoDB credentials are never exposed to React
- Redis credentials are never exposed to React
- AI actions are restricted to an allowlist
- Backend validates every AI action
- Payment status is verified server-side
- Webhooks are verified
- Duplicate actions are prevented
- Recovery attempts are bounded
- Successful payments automatically stop recovery

---

# 📈 Business Value

The system changes the workflow from:

```text
Payment Failed
      ↓
Business loses revenue
```

to:

```text
Payment Failed
      ↓
AI identifies recovery opportunity
      ↓
Recovery strategy
      ↓
Automated intervention
      ↓
Payment recovered
      ↓
Revenue measured
```

The key business metric is not:

> "How many AI decisions did we make?"

It is:

> **"How much revenue did we actually recover?"**

---

# 🏆 Why This Fits Razorpay Buildathon Track 03

The project directly addresses the core Track 03 direction:

```text
Detect revenue at risk
        ↓
Diagnose the problem
        ↓
Choose the right intervention
        ↓
Execute a bounded workflow
        ↓
Recover money
```

It also includes:

- Compliant escalation
- Stopping rules
- Audit trail
- Measured recovery
- Automated intervention

The current implementation focuses on:

> **Payment degradation → root cause/context → recovery action**

The same architecture can later support:

- Checkout abandonment
- Failed subscriptions
- B2B receivables
- Mandate retry sequences
- Promise-to-pay tracking
- Voice recovery

---

# 🌟 What Makes This Different?

### Traditional Payment System

```text
Payment Failed
      ↓
Show Error
```

### Simple Reminder System

```text
Payment Failed
      ↓
Send Reminder
```

### Our AI Revenue Recovery Agent

```text
Payment Failed
      ↓
Understand Context
      ↓
AI Decision
      ↓
Policy Validation
      ↓
Controlled Tool
      ↓
Automated Follow-up
      ↓
Payment Verification
      ↓
Recover / Escalate / Stop
```

The system creates a **closed recovery loop**.

---

# 🔮 Future Scope

The architecture can be extended to:

- Checkout abandonment recovery
- Subscription payment recovery
- B2B receivables
- SMS recovery
- WhatsApp recovery
- Voice-based recovery
- Multi-language recovery
- Multiple payment providers
- A/B testing recovery strategies
- Advanced customer segmentation
- Business-level recovery optimization

---

# 📌 Quick Links

| Application | Project Path | Live Link |
|---|---|---|
| 🛒 E-commerce | `/ecommerce` | https://ecommerce-eight-psi-85.vercel.app/ |
| 🤖 AI Revenue Recovery | `/ai-revenue-recovery` | https://ai-revenue-recovery-eight.vercel.app/ |
| 👤 Escalations | `/ai-revenue-recovery` | https://ai-revenue-recovery-eight.vercel.app/escalations |

---

# 🎯 One-Line Summary

> **An AI agent that detects failed payments, understands context, chooses and executes a safe recovery strategy, automates follow-ups, verifies payment success, and measures the revenue actually recovered.**

---

# 🏁 Final Flow

```text
              DETECT
                 ↓
             UNDERSTAND
                 ↓
               DECIDE
                 ↓
                ACT
                 ↓
             AUTOMATE
                 ↓
              VERIFY
                 ↓
       ┌─────────┼─────────┐
       ↓         ↓         ↓
   RECOVERED  ESCALATED  STOPPED
       ↓
   💰 REVENUE
   RECOVERED
```

> **Most payment systems stop when a payment fails.  
> Our system starts working.**
