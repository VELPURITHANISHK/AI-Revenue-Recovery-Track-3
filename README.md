# AI Revenue Recovery Agent

An AI-powered agent that detects failed payments, understands customer and payment context, chooses a recovery strategy, executes controlled actions, automates follow-ups, and verifies actual revenue recovery.

## Problem

Every failed payment is lost revenue. Most payment systems stop exactly when a payment fails. Businesses are left relying on manual data pulls, static cron jobs, and generic email spam to try and win the customer back. This results in high friction for the user and lost ROI for the business.

## Solution

Our system starts recovery after failure. The AI Revenue Recovery Agent automatically identifies failed payments in real-time, understands the customer's lifetime value and failure reason, decides the best recovery path, executes backend tools safely, automates the follow-up timeline, and measures actual revenue recovered. 

## Why an AI Agent?

This is more than a chatbot. It is a fully autonomous operational agent leveraging:
**LLM + Context + Decision Making + Tool Calling + Automation + Feedback/Payment Verification**

Unlike rigid rules (e.g., "send an email 2 days later"), the AI adapts. It understands the difference between a high-value loyal customer whose card expired, and a new customer repeatedly triggering fraud blocks. It selects the strategy, triggers automation, and stops itself the millisecond a payment succeeds.

## Architecture

`mermaid
flowchart TD
    Customer -->|Checkout| Ecom[E-commerce App]
    Ecom -->|Payment Attempt| RZP[Razorpay]
    RZP -->|Webhook: payment.failed| DB[(MongoDB)]
    DB --> Worker[BullMQ Worker]
    Worker --> Agent[OpenAI Agent]
    Agent -->|Strategy JSON| Policy[Backend Policy Layer]
    Policy -->|Validate & Execute| Tools[Tool Executor]
    Tools -->|Send Link / Email| Customer
    Tools -->|Schedule Next Check| Redis[(Redis Queue)]
    RZP -->|Webhook: payment.captured| DB
`

## Core Workflow

Payment Failed &rarr; Detect &rarr; Understand &rarr; AI Decision &rarr; Policy Validation &rarr; Tool Execution &rarr; Automation &rarr; Payment Verification &rarr; Recovered / Escalated / Stopped

## Features

- **Razorpay Test Mode:** Simulates real payment gateways and webhooks.
- **Failed payment detection:** Instant polling and webhook interception.
- **Customer context:** Retrieves lifetime value, past success/failures.
- **AI recovery strategy:** Dynamic selection (Gentle Reminder vs. Payment Link vs. Escalation).
- **Tool calling:** Pre-built safe backend functions.
- **Automated reminders:** Emails with contextual text.
- **BullMQ + Redis:** Fault-tolerant, distributed automation.
- **Human escalation:** AI intelligently passes high-risk cases to a human.
- **Safety limits:** Hardcoded maximums prevent AI hallucination loops.
- **Idempotency:** Real-time state checks prevent duplicate spam.
- **Revenue metrics:** Only confirmed Razorpay successes count towards recovered revenue.
- **Demo mode:** Accelerated timelines for live hackathon presentations.

## Tech Stack

**Frontend:** React, Vite, TailwindCSS
**Backend:** Node.js, Express
**Database:** MongoDB, Mongoose
**Payments:** Razorpay
**AI:** OpenAI
**Automation:** BullMQ, Redis

## Project Structure

- \ecommerce/\ - The mocked storefront and primary backend processing Razorpay orders.
- \i-revenue-recovery/\ - The autonomous AI agent, Dashboard UI, Worker, and Policy Engine.

## Setup

1. **Clone repository:**
   \git clone <repo-url>\
   \cd ai-revenue-recovery-project\

2. **Install dependencies:**
   \cd ecommerce/server && npm install\
   \cd ../client && npm install\
   \cd ../../ai-revenue-recovery/server && npm install\
   \cd ../client && npm install\

3. **Configure .env:**
   Copy \.env.example\ to \ecommerce/server/.env\ and \i-revenue-recovery/server/.env\ and add your keys.

4. **Start MongoDB & Redis:**
   Ensure local \mongod\ and \edis-server\ are running on default ports.

5. **Start the environment (in 4 terminals):**
   - Ecommerce Backend: \cd ecommerce/server && npm run dev\ (Port 5000)
   - Ecommerce Frontend: \cd ecommerce/client && npm run dev\ (Port 5173)
   - Recovery Backend & Worker: \cd ai-revenue-recovery/server && npm run dev\ (Port 5001)
   - Recovery Dashboard: \cd ai-revenue-recovery/client && npm run dev\ (Port 5174)

## Demo

**DEMO_MODE=true**
When set to true in the AI server's \.env\, the worker uses 10-second delays instead of 24-hour delays, and unlocks a "Reset Demo" button on the UI.

**Sequence:**
1. Visit Ecommerce Store, add an item, and checkout.
2. In the Razorpay modal, select **Failure**.
3. Open the Recovery Dashboard. See the new active case.
4. Click "Run Full Recovery Demo" to see the AI analyze the context and execute a tool.
5. Watch the Live Activity log schedule the follow-up.
6. Click "Simulate Customer Paid" to trigger the success webhook. 
7. Watch the Recovered Revenue instantly increase.

## Safety

- **Backend validation:** AI outputs JSON; backend validates the schema.
- **Tool allowlist:** AI can only call pre-defined functions.
- **Maximum attempts:** Hardcoded limits (e.g., 3 retries max) prevent loops.
- **Idempotency:** Re-running a webhook or queue job verifies the DB state first.
- **Secret protection:** API keys never touch the React frontend.
- **Isolation:** AI cannot execute arbitrary raw code or database queries.

## Business Impact

- **Revenue at Risk:** Total value of currently failed carts.
- **Recovered Revenue:** Only calculated from payments that successfully transitioned to CAPTURED.
- **Recovery Rate:** Percentage of failed value successfully salvaged.
- **Average Recovery Time:** Time from failure to capture.

## Future Scope

- SMS/WhatsApp recovery links via Twilio.
- Multi-language context injection for global customers.
- Advanced predictive churn modeling.
- A/B testing of different AI system prompts.
