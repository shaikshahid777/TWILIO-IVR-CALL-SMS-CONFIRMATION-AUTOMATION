# 📞 Twilio IVR Call & SMS Confirmation Automation

> **End-to-end voice + SMS automation built with n8n Cloud and Twilio**

[![n8n](https://img.shields.io/badge/n8n-Cloud-orange?logo=n8n)](https://n8n.io/)
[![Twilio](https://img.shields.io/badge/Twilio-Voice%20%26%20Messaging-F22F46?logo=twilio)](https://www.twilio.com/)
[![Workflow](https://mohammad-shaheed.app.n8n.cloud/assistant/88918ebe-6578-4a62-8eb1-a6458a8e74dc-blue)]()
[![Status](https://img.shields.io/badge/Status-Assessment%20Project-success)]()

## 🧭 Overview

This project implements a complete **Interactive Voice Response (IVR) and SMS confirmation workflow** using **n8n Cloud** and **Twilio APIs**.

The automation:

1. Starts an outbound call through the Twilio Calls API.
2. Returns TwiML that greets the recipient and asks them to **press 1**.
3. Captures the DTMF keypad response through a Twilio webhook.
4. Routes the response through an n8n **IF** node.
5. Sends a confirmation SMS when the recipient presses **1**.
6. Returns a spoken confirmation and records execution details in n8n/Twilio logs.

This repository contains the workflow export, project report, and demonstration recording used for the assessment submission.

---

## 🏗️ Architecture

```text
┌────────────────────┐
│   n8n Manual       │
│      Trigger       │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│ HTTP Request       │
│ Twilio Calls API   │
└─────────┬──────────┘
          │
          ▼
     ☎️ OUTBOUND CALL
          │
          ▼
┌────────────────────┐
│ n8n Answer Webhook  │
│ Returns TwiML       │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│ <Say> + <Gather>   │
│ "Press 1..."       │
└─────────┬──────────┘
          │
      DTMF = 1
          │
          ▼
┌────────────────────┐
│ n8n Digits Webhook │
│ Digits / CallSid   │
│ From / To          │
└─────────┬──────────┘
          │
          ▼
   ┌───────────────┐
   │  Pressed 1 ?  │
   └──────┬───┬────┘
          │YES│NO
          ▼   ▼
   ┌─────────┐ ┌──────────────┐
   │ Twilio  │ │ Not Confirmed│
   │ SMS API │ │ Response     │
   └────┬────┘ └──────────────┘
        │
        ▼
   ✅ Confirmation
        SMS
```

---

## 🔄 Workflow Components

| Component | Purpose |
|---|---|
| **Manual Trigger** | Starts the test call workflow from n8n |
| **HTTP Request — Calls API** | Creates the outbound Twilio voice call |
| **Answer Webhook** | Receives Twilio's request and returns TwiML |
| **TwiML Response** | Defines greeting and IVR behavior |
| **`<Gather>`** | Captures one DTMF keypad digit |
| **Digits Webhook** | Receives keypad/call metadata |
| **IF — Pressed 1?** | Routes confirmation vs. non-confirmation |
| **HTTP Request — Messages API** | Sends confirmation SMS |
| **Respond to Webhook** | Returns final call instructions |
| **n8n Execution History** | Provides workflow-level verification |

---

## 🧩 Core TwiML

The IVR uses TwiML to provide the call instructions:

```xml
<Response>
  <Gather numDigits="1"
          action="YOUR_N8N_WEBHOOK_URL"
          method="POST"
          timeout="10">
    <Say>Hello. Press 1 to confirm your appointment.</Say>
  </Gather>

  <Say>We did not receive any input. Goodbye.</Say>
</Response>
```

### TwiML elements used

- **`<Response>`** — root container for Twilio call instructions.
- **`<Say>`** — text-to-speech instruction.
- **`<Gather>`** — collects keypad/DTMF input and posts it to a webhook.

---

## 🔐 Credentials & Security

The workflow authenticates to Twilio using **HTTP Basic Authentication** with:

- Account SID
- Auth Token

Secrets are intentionally **not documented in this repository**.

### Security practices

- Do not commit Twilio Auth Tokens or API keys.
- Keep credentials inside n8n Credentials.
- Use HTTPS webhook endpoints.
- Validate Twilio's `X-Twilio-Signature` in production.
- Avoid exposing real recipient numbers in public screenshots.
- Replace sample values with your own credentials locally.

> **Never store production secrets in workflow JSON, README files, screenshots, or public Git history.**

---

## 🧪 Expected Execution

### Successful confirmation path

```text
Manual Trigger
      ↓
Twilio Call API
      ↓
Outbound Call
      ↓
TwiML Greeting
      ↓
Recipient presses 1
      ↓
Digits Webhook
      ↓
IF: Digits == "1"
      ↓
Twilio Messages API
      ↓
Confirmation SMS
      ↓
Successful execution
```

### Non-confirmation path

If the received digit is not `1`, the workflow follows the non-confirmation branch and returns a spoken message instead of sending the confirmation SMS.

---

## 📦 Repository Contents

| File | Description |
|---|---|
| `Twilio IVR Call & SMS Confirmation.json` | Exported n8n workflow |
| `Twilio_IVR_Call_SMS_Confirmation_Automation_Report.pdf` | Formal assessment report |
| `Screenrecording_20260918_180732.mp4` | Project demonstration recording |

---

## ▶️ How to Import & Run

### Prerequisites

- n8n Cloud or self-hosted n8n
- Twilio account
- Twilio phone number with the required capabilities
- Verified/eligible destination number for testing where required
- Public HTTPS URLs for n8n webhooks

### Setup

1. Import the JSON workflow into n8n.
2. Create an HTTP Basic Auth credential containing your Twilio Account SID and Auth Token.
3. Update the recipient number and Twilio sender number with your own test values.
4. Confirm the webhook URLs match your n8n environment.
5. Activate the workflow if required for live webhook execution.
6. Trigger **Start Call**.
7. Answer the call and press **1**.
8. Inspect the **Twilio Digits Webhook**, **Pressed 1?**, and **Send Confirmation SMS** executions.
9. Verify the call/message activity in the Twilio Console.

---

## 📊 Verification Checklist

- [x] Twilio Console configured
- [x] Twilio voice call request implemented
- [x] TwiML IVR implemented
- [x] DTMF input collection implemented
- [x] n8n webhook processing implemented
- [x] Conditional routing implemented
- [x] SMS confirmation implemented
- [x] n8n execution monitoring documented
- [x] Project report included
- [x] Demonstration recording included

---

## ⚠️ Known Limitations

Twilio trial accounts can impose restrictions on calling and messaging, including destination verification and regional messaging requirements.

For **+91 Indian destinations**, SMS delivery may be restricted depending on the Twilio account/sender configuration. A Twilio messaging error such as **572006** may therefore indicate a provider-side delivery/configuration limitation rather than an n8n logic failure.

When a delivery error occurs:

1. Check the Twilio Console error details.
2. Verify the destination and sender configuration.
3. Check trial-account restrictions and recipient verification requirements.
4. Review the Twilio message logs.
5. Record the limitation accurately in assessment evidence.

---

## 🛡️ Production Hardening

For a production deployment, the workflow can be extended with:

- Twilio webhook signature validation
- Input sanitization and validation
- Retry/error handling
- Structured logging
- Call status callbacks
- SMS delivery-status callbacks
- Idempotency protection
- Centralized configuration
- Environment-specific credentials
- Observability and alerting

---

## 🎥 Demonstration

The repository includes a screen recording demonstrating the project workflow.

**Recording:** `Screenrecording_20260918_180732.mp4`

---

## 📄 Documentation

The formal assignment submission report is included in the repository as:

**Twilio IVR Call & SMS Confirmation Automation — Assignment Submission Report**

---

## 🎯 Assessment Coverage

This implementation addresses the core assessment concepts:

**Credential Management → REST API Integration → Outbound Voice Automation → TwiML → IVR → DTMF → Webhooks → Conditional Routing → SMS Automation → Execution Monitoring → Security Awareness**

---

## 👤 Project

**Author:** Shaik Mohammad Shaheed  
**Repository:** `TWILIO-IVR-CALL-SMS-CONFIRMATION-AUTOMATION`

Built as an n8n automation assessment project combining programmable voice, webhooks, DTMF interaction, and SMS synchronization.
