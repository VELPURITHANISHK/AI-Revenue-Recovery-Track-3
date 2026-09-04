# API Documentation

The project uses two separate backend services communicating with a shared MongoDB instance.

## E-commerce API (Port 5000)

### 1. Create Order
- **METHOD:** POST
- **ENDPOINT:** `/api/payments/create-order`
- **PURPOSE:** Initialize a Razorpay checkout session and save the cart to MongoDB.
- **REQUEST:** `{ customer: { email, name, phone }, items: [ { productId, quantity } ] }`
- **RESPONSE:** `{ orderId, razorpayOrderId, amount, currency, keyId }`
- **IMPORTANT VALIDATION:** Validates product stock and calculates total amount server-side to prevent client-side tampering.

### 2. Verify Payment
- **METHOD:** POST
- **ENDPOINT:** `/api/payments/verify`
- **PURPOSE:** Validate Razorpay frontend success callback.
- **REQUEST:** `{ razorpay_order_id, razorpay_payment_id, razorpay_signature, order_id }`
- **RESPONSE:** `{ success: true, message: 'Payment verified successfully' }`
- **IMPORTANT VALIDATION:** Cryptographically verifies the SHA256 HMAC signature using `RAZORPAY_KEY_SECRET`.

### 3. Razorpay Webhook (Server-to-Server)
- **METHOD:** POST
- **ENDPOINT:** `/api/payments/webhook`
- **PURPOSE:** Idempotent background listener for Razorpay events (`payment.captured`, `payment.failed`).
- **REQUEST:** Razorpay webhook payload
- **RESPONSE:** `200 OK`
- **IMPORTANT VALIDATION:** Verifies `x-razorpay-signature` header. Handles both native Order checkouts and standalone Payment Links via `notes.paymentId`.

---

## AI Recovery API (Port 5001)

### 4. Get Dashboard Stats
- **METHOD:** GET
- **ENDPOINT:** `/api/recovery/stats`
- **PURPOSE:** Fetch real-time aggregate metrics for the dashboard.
- **REQUEST:** None
- **RESPONSE:** `{ revenueAtRisk, recoveredRevenue, recoveryRate, pipeline, strategies }`
- **IMPORTANT VALIDATION:** `recoveredRevenue` is exclusively calculated from cases marked `RECOVERED` in the database.

### 5. Analyze Payment
- **METHOD:** POST
- **ENDPOINT:** `/api/recovery/cases/:id/analyze`
- **PURPOSE:** Trigger the AI agent to analyze a failed payment and return a strategy decision.
- **REQUEST:** None (ID in URL)
- **RESPONSE:** `{ success: true, decision: {...} }`
- **IMPORTANT VALIDATION:** Enforces OpenAI JSON schema generation. Backend Policy Engine intercepts and overrides the decision if maximum retries/reminders are exceeded.

### 6. Start Recovery Automation
- **METHOD:** POST
- **ENDPOINT:** `/api/recovery/cases/:id/start`
- **PURPOSE:** Add the case to the BullMQ / Redis queue.
- **REQUEST:** None
- **RESPONSE:** `{ success: true }`
- **IMPORTANT VALIDATION:** Rejects if the case is already recovered or already active in the queue.

### 7. Simulate Payment Success (Demo Only)
- **METHOD:** POST
- **ENDPOINT:** `/api/recovery/cases/:id/simulate-success`
- **PURPOSE:** Simulate a successful webhook for demo purposes when Razorpay test mode is unavailable.
- **REQUEST:** None
- **RESPONSE:** `{ success: true }`
- **IMPORTANT VALIDATION:** Only functions if `DEMO_MODE=true` is set in the environment.

### 8. Health Check
- **METHOD:** GET
- **ENDPOINT:** `/api/health`
- **PURPOSE:** Validate container/environment health.
- **REQUEST:** None
- **RESPONSE:** `{ API: "OK", MongoDB: "Connected", Redis: "Connected", Worker: "Running" }`
- **IMPORTANT VALIDATION:** Safely exposes connection status without leaking credentials.
