# System Architecture

## A. High-Level Architecture

```mermaid
flowchart TD
    subgraph Frontend
        Store[E-commerce Store UI]
        Dash[AI Recovery Dashboard]
    end

    subgraph Backend
        API[E-commerce Node.js API]
        RecoveryAPI[Recovery Node.js API]
        Worker[BullMQ Background Worker]
        Decision[Decision & Policy Engine]
    end

    subgraph External Services
        RZP[Razorpay]
        LLM[OpenAI GPT-4o]
        Email[Email Provider]
    end

    subgraph Data Layer
        DB[(MongoDB Shared DB)]
        Redis[(Redis Queue)]
    end

    Store -->|Checkout| API
    API -->|Create Order| RZP
    RZP -->|Webhook| API
    API -->|Save State| DB
    
    DB --> Worker
    Worker -->|Fetch Context| Decision
    Decision -->|Analyze| LLM
    Decision -->|Execute| Email
    Decision -->|Schedule| Redis
    Redis --> Worker

    Dash -->|View Metrics| RecoveryAPI
    RecoveryAPI --> DB
```

## B. AI Decision Flow

```mermaid
sequenceDiagram
    participant Worker as BullMQ Worker
    participant Context as Context Builder
    participant Agent as OpenAI Agent
    participant Policy as Backend Policy Layer
    participant Tools as Tool Executor

    Worker->>Context: Fetch Payment & Customer History
    Context-->>Worker: JSON Context Data
    Worker->>Agent: Prompt + JSON Context + Allowed Tools
    Agent-->>Worker: Structured JSON Decision
    Worker->>Policy: Validate Action vs Limits
    alt Safe & Within Limits
        Policy->>Tools: Execute Tool (e.g. sendReminder)
        Tools-->>Worker: Success Result
    else Unsafe or Max Limits Reached
        Policy->>Tools: Force ESCALATE_TO_HUMAN
        Tools-->>Worker: Escalate Result
    end
```

## C. Recovery Automation Flow

```mermaid
flowchart LR
    Start([Payment Fails]) --> Schedule[Add Job to Redis]
    Schedule --> Wait[Wait 10s (Demo) / 24h (Prod)]
    Wait --> Pop[Worker Pops Job]
    Pop --> CheckDB{Already Paid?}
    CheckDB -->|Yes| Halt([Halt Recovery])
    CheckDB -->|No| AI[AI Decision & Execution]
    AI --> UpdateDB[Update Recovery Case]
    UpdateDB --> CheckLimits{Max Limits?}
    CheckLimits -->|Yes| Escalate([Escalate / Stop])
    CheckLimits -->|No| Schedule
```

## D. Payment Success Flow (Idempotency)

```mermaid
sequenceDiagram
    participant Customer
    participant RZP as Razorpay
    participant Webhook as Webhook Listener
    participant DB as MongoDB
    participant Worker as Background Worker

    Customer->>RZP: Completes Payment via Link
    RZP->>Webhook: POST /api/payments/webhook (payment.captured)
    Webhook->>DB: Update Payment Status = CAPTURED
    Note over Worker: Time passes... Scheduled job triggers
    Worker->>DB: Fetch Payment Status
    DB-->>Worker: Status: CAPTURED
    Worker->>Worker: Abort AI Processing
    Worker->>DB: Mark RecoveryCase = RECOVERED
    Worker->>Worker: Cancel pending BullMQ Jobs
```
