# Judge Quick Guide

### What makes this an AI Agent?
It operates autonomously to achieve a goal (revenue recovery). It analyzes environment context (database history), selects from a suite of available tools (emails, links, escalations), executes them, and schedules its own follow-ups using persistent message queues (BullMQ).

### What does AI decide?
The AI decides the optimal **recovery strategy**. It categorizes the customer's risk profile and outputs a strictly typed JSON decision recommending an action like SEND_REMINDER, CREATE_PAYMENT_LINK, or ESCALATE_TO_HUMAN.

### What tools can AI call?
- sendReminder (Dispatches contextual email)
- createPaymentLink (Calls Razorpay API for direct links)
- etryPayment (Background token retry, if supported)
- escalateToHuman (Flags case for manual review)
- stopRecovery (Halts automated outreach)

### How is AI prevented from causing unsafe actions?
1. **No direct execution:** AI only outputs a JSON string recommendation.
2. **Deterministic Policy Engine:** A Node.js layer intercepts the JSON and validates it against hard business rules.
3. **Strict Limits:** If AI recommends a 4th retry but MAX_RETRIES is 3, the backend overrides the AI and forces a stop/escalation.

### How is recovered revenue calculated?
It is calculated by securely querying MongoDB for RecoveryCase documents tied to payments where the final status successfully transitioned to CAPTURED via Razorpay Webhooks. It is **never** based on AI predictions or probabilities.

### Why BullMQ and Redis?
Standard setTimeout timers are destroyed if the Node server restarts. BullMQ backed by Redis ensures that scheduled recovery follow-ups (e.g., checking status in 24 hours) survive server crashes and can scale across multiple worker nodes.

### Why Razorpay?
Razorpay provides an excellent developer experience, robust webhooks, and a comprehensive Test Mode environment, which is perfect for simulating both failures and asynchronous successes securely.

### How does the system scale?
By decoupling the E-commerce frontend from the AI worker. The Node.js Webhook listener simply drops tasks into Redis. Dozens of isolated BullMQ worker instances can be spun up independently to consume the queue and process AI decisions horizontally.

### What happens when payment succeeds during recovery?
The exact millisecond the Razorpay payment.captured webhook arrives, the payment status updates in MongoDB. The next time the BullMQ worker runs, it reads the live DB state. Seeing CAPTURED, it instantly halts the job and marks the case as RECOVERED.

### What happens when AI fails?
If the OpenAI API times out, returns invalid JSON, or hallucinated a non-existent tool, the Backend Policy catches the error and executes a safe fallback (e.g., waiting and retrying, or escalating to human).

### What happens after maximum attempts?
The backend intercepts the action, changes the status to STOPPED or ESCALATED, and refuses to schedule any further BullMQ jobs for that paymentId.

### What makes this different from a normal payment reminder?
Cron-job reminders are static ("Send Email 1 on Day 2"). This agent is contextual. It can immediately generate a direct payment link for a loyal customer making a typo, while instantly escalating a high-value cart repeatedly failing fraud checks.
