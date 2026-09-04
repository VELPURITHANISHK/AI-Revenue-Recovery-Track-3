# 3-Minute Hackathon Demo Script

## 0:00–0:20 - Problem
**Visual:** Slide 1 & 2 (Problem Statement).
**Speaker:** "Every failed payment is potential lost revenue. Most payment systems stop exactly when a payment fails. Businesses then have to manually identify the customer, decide what to do, send generic reminders, and follow up. We built an AI agent that starts working exactly at that point."

## 0:20–0:40 - Solution
**Visual:** Slide 3 (Our Solution).
**Speaker:** "Our AI Revenue Recovery Agent automatically detects failed payments, understands why the payment failed and who the customer is, decides the appropriate recovery strategy, acts by executing controlled backend tools, and automates follow-ups to recover the revenue."

## 0:40–1:00 - Architecture
**Visual:** Slide 4 (Architecture Diagram).
**Speaker:** "Here is how it works: The E-commerce app processes through Razorpay and saves to MongoDB. Our Agent detects the failure, gathers context, and makes a decision. The AI does not directly control infrastructure. A Backend Policy Layer validates every decision before the Tool Executor schedules jobs in BullMQ and Redis. It's completely secure."

## 1:00–2:30 - Live Demo
**Visual:** Switch to E-Commerce Store UI. Add item to cart, trigger Razorpay modal, click "Failure".
**Speaker:** "Let's look at the live pipeline. A customer just attempted a ₹3499 payment, and it failed."

**Visual:** Switch to Recovery Dashboard. Point out the new active case. Click "Run Full Recovery Demo".
**Speaker:** "I'll trigger the AI. Look at the Decision Card: It identified this as a 'Loyal Customer' with a 'Low Risk' failure. The AI selected a 'Gentle Reminder' strategy."

**Visual:** Scroll to Live Activity Log / Timeline.
**Speaker:** "The Backend validated this action, executed the email tool, and BullMQ automatically scheduled a follow-up check."

**Visual:** Click "Simulate Customer Paid" button.
**Speaker:** "Now, let's simulate the customer checking their email and completing the payment. Instantly, the automation stops. The status changes to 'Payment Captured', and exactly ₹3499 is added to our Recovered Revenue metrics without any manual input."

## 2:30–2:50 - Safety + AI Explanation
**Visual:** Switch to Case View of a High-Value Failure (pre-prepared or simulated).
**Speaker:** "This isn't a chatbot. The AI handles operational workflows. If a customer fails a ₹25,000 cart for the third time, the AI detects 'High Risk' and immediately forces a human escalation. Because it handles money, the AI cannot execute arbitrary code. Our backend enforces maximum retries, prevents duplicates, and halts everything the millisecond a payment succeeds."

## 2:50–3:00 - Closing
**Visual:** Final Slide / Dashboard overview.
**Speaker:** "Instead of treating a payment failure as the end of a transaction, we treat it as the beginning of a recovery process. We don't just detect lost revenue. We actively recover it. Thank you."
