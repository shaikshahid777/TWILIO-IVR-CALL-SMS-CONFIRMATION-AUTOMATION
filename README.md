# 📞 Twilio IVR Call & SMS Confirmation Automation

> **Production-style n8n automation assessment — programmable voice, IVR, DTMF, webhooks and SMS synchronization.**

<p align="center">

[![▶ View Demo](https://img.shields.io/badge/▶%20View%20Demo-Loom-625DF5?style=for-the-badge&logo=loom&logoColor=white)](https://www.loom.com/share/6b9e96648b5d417eb01f4575389f815d)
[![⚙ Open Workflow](https://img.shields.io/badge/⚙%20Open%20Workflow-n8n-FF6D5A?style=for-the-badge&logo=n8n&logoColor=white)](https://mohammad-shaheed.app.n8n.cloud/workflow/IvKfTEs5ja0fN8DV)
[![📄 Project Report](https://img.shields.io/badge/📄%20Project%20Report-PDF-DC2626?style=for-the-badge&logo=adobeacrobatreader&logoColor=white)](./Twilio_IVR_Call_SMS_Confirmation_Automation_Report.pdf)
[![⬇ Workflow JSON](https://img.shields.io/badge/⬇%20Workflow%20JSON-Download-111827?style=for-the-badge&logo=json&logoColor=white)](./Twilio%20IVR%20Call%20%26%20SMS%20Confirmation.json)

</p>

<p align="center">

[![n8n](https://img.shields.io/badge/n8n-Cloud-FF6D5A?style=flat-square&logo=n8n&logoColor=white)](https://n8n.io/)
[![Twilio](https://img.shields.io/badge/Twilio-Voice%20%26%20SMS-F22F46?style=flat-square&logo=twilio&logoColor=white)](https://www.twilio.com/)
[![Status](https://img.shields.io/badge/Status-Assessment%20Project-success?style=flat-square)](#)

</p>

---

## 🚀 Project Overview

This project implements an **Interactive Voice Response (IVR) + SMS confirmation workflow** using **n8n Cloud** and the **Twilio REST APIs**.

The workflow demonstrates an end-to-end automation lifecycle:

**Trigger → Outbound Call → TwiML → IVR → DTMF → Webhook → Conditional Routing → SMS → Execution Monitoring**

### What the automation does

1. Starts an outbound Twilio voice call from n8n.
2. Twilio requests instructions from an n8n webhook.
3. n8n returns TwiML containing a spoken IVR prompt.
4. The recipient is asked to **press 1**.
5. Twilio sends the DTMF response to a second n8n webhook.
6. n8n evaluates the received digit.
7. When the digit is `1`, the workflow triggers the Twilio Messages API.
8. A confirmation SMS is sent and the workflow execution can be reviewed in n8n/Twilio logs.

---

## 🎯 Assessment Objectives Covered

| Requirement | Implementation |
|---|---|
| Twilio account & phone configuration | Twilio Console |
| Outbound voice call | Twilio Calls API |
| Authentication | n8n HTTP Basic Auth credential |
| IVR prompt | TwiML `<Say>` |
| DTMF collection | TwiML `<Gather>` |
| Webhook processing | n8n Webhook nodes |
| Conditional logic | n8n IF node |
| SMS confirmation | Twilio Messages API |
| Execution monitoring | n8n Executions |
| Security awareness | HTTPS + `X-Twilio-Signature` guidance |

---

## 🏗️ Architecture

```text
┌──────────────────────┐
│     n8n Manual       │
│       Trigger        │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Twilio Calls API     │
│ HTTP Request          │
└──────────┬───────────┘
           │
           ▼
       ☎ OUTBOUND CALL
           │
           ▼
┌──────────────────────┐
│ Twilio Answer Webhook│
│ n8n → TwiML Response │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ <Say> + <Gather>     │
│ "Press 1 to confirm" │
└──────────┬───────────┘
           │
        DTMF = 1
           │
           ▼
┌──────────────────────┐
│ Twilio Digits Webhook│
│ Digits / CallSid     │
│ From / To            │
└──────────┬───────────┘
           │
           ▼
      ┌─────────────┐
      │ Pressed 1 ? │
      └──────┬──────┘
          YES│  │NO
             │  │
             ▼  ▼
     ┌──────────┐ ┌────────────────┐
     │ SMS API  │ │ Not Confirmed  │
     └────┬─────┘ │ TwiML Response │
          │       └────────────────┘
          ▼
   📱 Confirmation SMS
```

---

## 🔄 Workflow Components

| Node | Role |
|---|---|
| **Start Call** | Manual entry point for testing |
| **Initiate Twilio Call** | Creates the outbound voice call |
| **Twilio Answer Webhook** | Receives Twilio's call request |
| **Return IVR TwiML** | Returns the voice/IVR instructions |
| **Twilio Digits Webhook** | Receives DTMF and call metadata |
| **Pressed 1?** | Routes the confirmation path |
| **Respond Confirmed** | Returns confirmation TwiML |
| **Send Confirmation SMS** | Sends the confirmation message through Twilio |
| **Respond Not Confirmed** | Handles invalid/non-confirmation input |

---

## 🧩 TwiML IVR

The IVR is built with Twilio Markup Language (TwiML):

```xml
<Response>
  <Gather numDigits="1"
          action="YOUR_N8N_DIGITS_WEBHOOK"
          method="POST"
          timeout="10">
    <Say voice="alice">
      Hello. Press 1 to confirm your appointment.
    </Say>
  </Gather>

  <Say voice="alice">
    We did not receive any input. Goodbye.
  </Say>
</Response>
```

### TwiML elements

- **`<Response>`** — root container for Twilio call instructions.
- **`<Say>`** — converts text into spoken audio.
- **`<Gather>`** — collects keypad/DTMF input and posts it to the configured webhook.
- **`<Dial>`** — a TwiML capability that can connect a call to another number; it is part of the assessment concepts but is not required by this implementation.

---

## 🔐 Credentials & Security

The workflow uses **HTTP Basic Authentication** in n8n for Twilio API requests.

### Credentials

- Twilio Account SID
- Twilio Auth Token
- Twilio phone number / sender configuration

**No Auth Token or other secret is intentionally stored in this repository.**

### Security checklist

- ✅ Keep Twilio credentials inside n8n Credentials.
- ✅ Use HTTPS webhook endpoints.
- ✅ Never commit Auth Tokens/API keys.
- ✅ Avoid publishing real recipient numbers in screenshots.
- ⚠️ For production, validate Twilio's `X-Twilio-Signature`.
- ⚠️ Review webhook input validation and replay/idempotency controls.

> **Important:** The Twilio Console URL supplied for testing is account-specific. It is intentionally **not embedded in this public README**. Use the Twilio Console directly after signing in.

---

## 🧪 Expected Execution

### Confirmation path

```text
Start Call
   ↓
Twilio Calls API
   ↓
Outbound Voice Call
   ↓
Answer Webhook
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
```

### Non-confirmation path

If the received digit is not `1`, the workflow follows the non-confirmation branch and returns a spoken response instead of sending the confirmation SMS.

---

## 📂 Repository Contents

| Resource | Purpose |
|---|---|
| [⚙️ n8n Workflow](https://mohammad-shaheed.app.n8n.cloud/workflow/IvKfTEs5ja0fN8DV) | Live workflow in n8n Cloud |
| [▶️ Loom Demo](https://www.loom.com/share/6b9e96648b5d417eb01f4575389f815d) | Project demonstration |
| [📄 Assignment Report](./Twilio_IVR_Call_SMS_Confirmation_Automation_Report.pdf) | Formal documentation |
| [⬇️ Workflow JSON](./Twilio%20IVR%20Call%20%26%20SMS%20Confirmation.json) | Importable n8n workflow export |
| [🎥 Local Recording](./Screenrecording_20260918_180732.mp4) | Repository copy of the demonstration recording |
| [📊 n8n Executions](https://mohammad-shaheed.app.n8n.cloud/workflow/IvKfTEs5ja0fN8DV/executions) | Workflow execution history |

---

## ▶️ Setup & Run

### Prerequisites

- n8n Cloud or self-hosted n8n
- Twilio account
- Twilio voice-capable number
- Messaging-capable Twilio sender where SMS testing is supported
- Public HTTPS webhook endpoints

### Steps

1. Download the [workflow JSON](./Twilio%20IVR%20Call%20%26%20SMS%20Confirmation.json).
2. Import it into n8n.
3. Create an HTTP Basic Auth credential using your own Twilio Account SID and Auth Token.
4. Configure your own recipient and sender values.
5. Verify the webhook URLs point to your n8n environment.
6. Activate the workflow.
7. Run **Start Call**.
8. Answer the call and press **1**.
9. Review the webhook and SMS executions.
10. Verify the corresponding activity in Twilio Console.

---

## 📊 Verification Checklist

- [x] Outbound Twilio call request
- [x] TwiML IVR response
- [x] DTMF input collection
- [x] n8n webhook processing
- [x] Conditional routing
- [x] SMS API integration
- [x] Execution monitoring
- [x] Assessment report
- [x] Demo recording

---

## ⚠️ Testing & Provider Limitations

Twilio trial accounts and regional messaging configurations can restrict calling or SMS delivery.

For **+91 Indian destinations**, SMS delivery can depend on the Twilio account, sender configuration, registration/compliance requirements, and destination eligibility. A Twilio error such as **572006** should be investigated in the Twilio Console rather than automatically treated as an n8n workflow failure.

For assessment evidence, distinguish clearly between:

- **Workflow execution success**
- **Twilio API request success/failure**
- **Actual SMS delivery status**

This keeps the project documentation technically accurate.

---

## 🛡️ Production Hardening

Possible production enhancements include:

- Twilio `X-Twilio-Signature` validation
- Strict webhook input validation
- Retry and error-handling strategy
- Call status callbacks
- SMS delivery-status callbacks
- Idempotency protection
- Structured logging
- Centralized configuration
- Environment-specific credentials
- Monitoring and alerting

---

## 🎥 Demo

**Primary demo:**  
[▶ Watch the Loom demonstration](https://www.loom.com/share/6b9e96648b5d417eb01f4575389f815d)

The Loom link is preferred for reviewers because it opens the demonstration directly without requiring them to download the repository's MP4 file.

---

## 📄 Documentation

**Formal report:**  
[📄 Open the Assignment Submission Report](./Twilio_IVR_Call_SMS_Confirmation_Automation_Report.pdf)

The report documents the project architecture, implementation flow, TwiML, webhook/security concepts, execution monitoring, limitations, and submission evidence.

---

## 👤 Author

**Shaik Mohammad Shaheed**

AI & Automation | n8n | API Integration | Webhooks | AI Agents | Generative AI

---

<p align="center">

**Built with n8n + Twilio**

[![GitHub](https://img.shields.io/badge/View%20Repository-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/shaikshahid777/TWILIO-IVR-CALL-SMS-CONFIRMATION-AUTOMATION)

</p>
