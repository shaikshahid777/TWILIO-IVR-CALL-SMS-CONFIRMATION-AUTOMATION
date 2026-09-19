# 📞 Twilio IVR Call & SMS Confirmation Automation

> **Production-style voice automation workflow built with n8n Cloud + Twilio**

<p align="center">
  <a href="https://www.loom.com/share/6b9e96648b5d417eb01f4575389f815d">
    <img src="https://img.shields.io/badge/%E2%96%B6%20Watch%20Demo-Loom-625DF5?style=for-the-badge&logo=loom&logoColor=white" alt="Watch Demo">
  </a>
  <a href="./Twilio%20IVR%20Call%20%26%20SMS%20Confirmation.json">
    <img src="https://img.shields.io/badge/%E2%9A%99%20n8n%20Workflow-JSON-FF6D5A?style=for-the-badge&logo=n8n&logoColor=white" alt="n8n Workflow">
  </a>
  <a href="./Twilio_IVR_Call_SMS_Confirmation_Automation_Report.pdf">
    <img src="https://img.shields.io/badge/%F0%9F%93%84%20Project%20Report-PDF-DC2626?style=for-the-badge&logo=adobeacrobatreader&logoColor=white" alt="Project Report">
  </a>
</p>

<p align="center">
  <a href="https://n8n.io/">n8n Cloud</a> ·
  <a href="https://www.twilio.com/">Twilio</a> ·
  <a href="https://www.loom.com/share/6b9e96648b5d417eb01f4575389f815d">Demo Video</a>
</p>

---

## 📌 Project Overview

This project demonstrates an end-to-end **Interactive Voice Response (IVR) + SMS confirmation automation** using **n8n Cloud** and **Twilio**.

The workflow:

1. Starts an outbound call through the Twilio Calls API.
2. Returns TwiML instructions to the recipient.
3. Uses `<Say>` to deliver the IVR prompt.
4. Uses `<Gather>` to capture one DTMF digit.
5. Receives the digit through an n8n webhook.
6. Routes the response with an IF condition.
7. Sends a confirmation SMS when the recipient presses **1**.
8. Returns a spoken confirmation and records execution activity in n8n.

### 🎯 Assessment Focus

**REST API Integration · TwiML · IVR · DTMF · Webhooks · Conditional Routing · SMS Automation · Execution Monitoring · Security**

---

## 🚀 Quick Access

| Resource | Access |
|---|---|
| 🎥 **Demo Video** | [Watch on Loom](https://www.loom.com/share/6b9e96648b5d417eb01f4575389f815d) |
| ⚙️ **n8n Workflow Export** | [Open JSON](./Twilio%20IVR%20Call%20%26%20SMS%20Confirmation.json) |
| 📄 **Assessment Report** | [Open PDF](./Twilio_IVR_Call_SMS_Confirmation_Automation_Report.pdf) |
| 🎬 **Local Screen Recording** | [Open MP4](./Screenrecording_20260918_180732.mp4) |
| 🏠 **n8n** | [n8n.io](https://n8n.io/) |
| ☁️ **Twilio** | [Twilio.com](https://www.twilio.com/) |

> **Note:** n8n workflow/editor pages, execution history, and Twilio Console pages require authentication. They are intentionally **not embedded as public evidence links** in this README.

---

## 🏗️ Architecture

```text
┌──────────────────────┐
│   n8n Manual Trigger │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Twilio Calls API     │
│ HTTP POST             │
└──────────┬───────────┘
           │
           ▼
       ☎️ OUTBOUND CALL
           │
           ▼
┌──────────────────────┐
│ Twilio → n8n Webhook │
│ Return IVR TwiML     │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ <Say> + <Gather>     │
│ "Press 1..."         │
└──────────┬───────────┘
           │
        DTMF = 1
           │
           ▼
┌──────────────────────┐
│ Digits Webhook       │
│ Digits / CallSid     │
│ From / To             │
└──────────┬───────────┘
           │
           ▼
      ┌─────────────┐
      │ Pressed 1 ? │
      └──────┬──────┘
         YES │  NO
             │
      ┌──────▼───────┐       ┌────────────────┐
      │ SMS API      │       │ Not Confirmed  │
      │ Confirmation │       │ TwiML Response │
      └──────┬───────┘       └────────────────┘
             │
             ▼
       📱 SMS Confirmation
```

---

## 🔄 Workflow Components

| Component | Purpose |
|---|---|
| **Manual Trigger** | Starts the test call workflow |
| **Twilio Calls API** | Creates the outbound voice call |
| **Answer Webhook** | Receives Twilio's call request |
| **Respond to Webhook** | Returns IVR TwiML instructions |
| **`<Say>`** | Converts text into spoken instructions |
| **`<Gather>`** | Captures one DTMF keypad digit |
| **Digits Webhook** | Receives keypad and call metadata |
| **IF — Pressed 1?** | Routes confirmation vs. non-confirmation |
| **Twilio Messages API** | Sends the confirmation SMS |
| **Execution History** | Supports workflow-level verification |

---

## 🧩 IVR TwiML

The call uses TwiML to define the IVR behavior:

```xml
<Response>
  <Gather
      numDigits="1"
      action="YOUR_N8N_WEBHOOK_URL"
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

### TwiML Elements

| Element | Role |
|---|---|
| `<Response>` | Root container for Twilio instructions |
| `<Say>` | Text-to-speech response |
| `<Gather>` | Collects keypad/DTMF input |
| `action` | Sends the gathered input to the n8n webhook |
| `method="POST"` | Sends webhook data using HTTP POST |

---

## 🔐 Credentials & Security

The workflow uses **HTTP Basic Authentication** for Twilio API requests.

Required credentials:

- Twilio Account SID
- Twilio Auth Token
- Twilio phone number / approved sender
- n8n webhook endpoints

### Security Rules

- **Never** commit Auth Tokens, API keys, or passwords.
- Store credentials inside **n8n Credentials**.
- Keep webhook endpoints on HTTPS.
- Validate **`X-Twilio-Signature`** in production.
- Redact phone numbers and account identifiers in public screenshots where appropriate.
- Do not publish authenticated console URLs as evidence links.

> **This repository intentionally excludes secret credentials.**

---

## 🧪 End-to-End Execution

### Confirmation path

```text
Manual Trigger
      ↓
Twilio Calls API
      ↓
Outbound Voice Call
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
Execution History
```

### Non-confirmation path

If the received digit is not `1`, the workflow follows the non-confirmation branch and returns a spoken response without sending the confirmation SMS.

---

## 📦 Repository Contents

| File | Purpose |
|---|---|
| [n8n Workflow JSON](./Twilio%20IVR%20Call%20%26%20SMS%20Confirmation.json) | Importable workflow export |
| [Assessment Report PDF](./Twilio_IVR_Call_SMS_Confirmation_Automation_Report.pdf) | Formal project documentation |
| [Demo Recording](./Screenrecording_20260918_180732.mp4) | Local demonstration recording |
| [README](./README.md) | Project overview and setup guide |

---

## ⚙️ Setup

### Prerequisites

- n8n Cloud or self-hosted n8n
- Twilio account
- Twilio phone number with required capabilities
- Public HTTPS n8n webhook URLs
- Eligible/verified destination number where required by Twilio

### Import

1. Download the [workflow JSON](./Twilio%20IVR%20Call%20%26%20SMS%20Confirmation.json).
2. Import it into n8n.
3. Create an HTTP Basic Auth credential containing your Twilio Account SID and Auth Token.
4. Configure your own recipient and sender values.
5. Verify the webhook URLs.
6. Activate the workflow for live webhook execution.
7. Trigger the **Start Call** node.
8. Answer the call and press **1**.
9. Review the relevant n8n executions and Twilio activity.

---

## ✅ Assessment Coverage

- [x] Twilio Console setup
- [x] Twilio Calls API integration
- [x] HTTP Basic Authentication
- [x] TwiML IVR response
- [x] `<Say>` implementation
- [x] `<Gather>` / DTMF collection
- [x] n8n webhook processing
- [x] Conditional routing
- [x] Twilio Messages API integration
- [x] Execution monitoring
- [x] Security considerations
- [x] Documentation
- [x] Demonstration video

---

## ⚠️ Known Limitations

Twilio trial accounts can impose restrictions on calling and messaging, including destination verification and regional messaging requirements.

For **+91 Indian destinations**, SMS delivery may depend on the Twilio account, sender configuration, and applicable messaging requirements. A provider error such as **572006** should be investigated in the Twilio Console rather than automatically treated as an n8n workflow failure.

Recommended troubleshooting:

1. Review the Twilio Console error details.
2. Verify the destination and sender configuration.
3. Check trial-account restrictions.
4. Review Twilio message logs.
5. Record the actual delivery result accurately in assessment evidence.

---

## 🛡️ Production Hardening

For production deployment, this workflow can be extended with:

- Twilio webhook signature validation
- Input validation and sanitization
- Retry and failure handling
- Call status callbacks
- SMS delivery-status callbacks
- Idempotency protection
- Structured logging
- Centralized configuration
- Environment-specific credentials
- Monitoring and alerting

---

## 🎥 Demonstration

**Primary demo:** [Watch the project on Loom](https://www.loom.com/share/6b9e96648b5d417eb01f4575389f815d)

The repository also contains the original local screen recording for assessment evidence.

---

## 👤 Author

**Shaik Mohammad Shaheed**

AI & Automation · n8n · REST APIs · Webhooks · AI Agents · Generative AI

Built as an automation assessment project demonstrating programmable voice, IVR interaction, DTMF processing, webhook-based routing, and SMS synchronization.

---

<p align="center">
  <strong>📞 Voice → 🎛️ DTMF → 🔀 Routing → 📱 SMS</strong>
</p>
