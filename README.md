# 📞 Twilio IVR Call & SMS Confirmation Automation

> **End-to-end IVR voice automation with DTMF confirmation and SMS API integration — built with n8n Cloud + Twilio.**

<p align="center">
  <a href="https://www.loom.com/share/6b9e96648b5d417eb01f4575389f815d"><img src="https://img.shields.io/badge/%E2%96%B6%20WATCH%20DEMO-625DF5?style=for-the-badge&logo=loom&logoColor=white" alt="Watch Demo"></a>
  <a href="./Twilio%20IVR%20Call%20%26%20SMS%20Confirmation.json"><img src="https://img.shields.io/badge/%E2%9A%99%20WORKFLOW%20JSON-FF6D5A?style=for-the-badge&logo=n8n&logoColor=white" alt="Workflow JSON"></a>
  <a href="./Twilio_IVR_Call_SMS_Confirmation_Automation_Report.pdf"><img src="https://img.shields.io/badge/%F0%9F%93%84%20PROJECT%20REPORT-DC2626?style=for-the-badge&logo=adobeacrobatreader&logoColor=white" alt="Project Report"></a>
</p>

<p align="center">
  <b>n8n</b> · <b>Twilio Voice</b> · <b>TwiML</b> · <b>DTMF</b> · <b>Webhooks</b> · <b>REST APIs</b> · <b>SMS API</b>
</p>

---

## 🎯 Project at a Glance

This assessment project implements an **Interactive Voice Response (IVR)** workflow that connects **n8n Cloud** with the **Twilio Voice API** and **Twilio Messaging API**.

The completed workflow demonstrates:

- ☎️ Outbound voice-call initiation
- 🗣️ TwiML-based voice instructions
- 🎛️ One-digit DTMF collection
- 🔗 n8n webhook processing
- 🔀 Conditional routing with an IF node
- ✅ Voice confirmation when **1** is pressed
- 📱 Twilio SMS API integration / delivery attempt
- 🔐 Credential and webhook security considerations
- 📊 n8n execution monitoring

> **Assessment result:** The IVR and DTMF confirmation flow worked successfully during testing. SMS delivery was not received during the trial-account test; the workflow still contains the Twilio Messages API integration and attempts the SMS request.

---

## 🚀 Quick Access

| Resource | Link |
|---|---|
| 🎥 **Demo Video** | [Watch on Loom](https://www.loom.com/share/6b9e96648b5d417eb01f4575389f815d) |
| ⚙️ **n8n Workflow JSON** | [View / Download](./Twilio%20IVR%20Call%20%26%20SMS%20Confirmation.json) |
| 📄 **Assessment Report** | [View PDF](./Twilio_IVR_Call_SMS_Confirmation_Automation_Report.pdf) |
| 🎬 **Screen Recording** | [Open MP4](./Screenrecording_20260918_180732.mp4) |
| 🌐 **n8n** | [Visit n8n](https://n8n.io/) |
| ☁️ **Twilio** | [Visit Twilio](https://www.twilio.com/) |

> **Private resources:** The n8n workflow/editor and execution-history pages and the Twilio Console require authentication. They are not presented as public evidence links.

---

## 🏗️ System Architecture

```text
                         ┌─────────────────────┐
                         │    n8n Manual       │
                         │      Trigger        │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │  Twilio Calls API   │
                         │     HTTP POST       │
                         └──────────┬──────────┘
                                    │
                                    ▼
                              ☎️ OUTBOUND CALL
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Twilio Answer       │
                         │ n8n Webhook         │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │      TwiML          │
                         │ <Say> + <Gather>    │
                         └──────────┬──────────┘
                                    │
                           Recipient presses 1
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ Twilio Digits       │
                         │ n8n Webhook         │
                         └──────────┬──────────┘
                                    │
                                    ▼
                            ┌───────────────┐
                            │  Pressed 1 ?  │
                            └───────┬───────┘
                               YES  │  NO
                                    │
                     ┌──────────────┴──────────────┐
                     ▼                             ▼
              ┌──────────────┐             ┌───────────────┐
              │ Confirmation │             │ Not Confirmed │
              │ TwiML Voice  │             │ TwiML Voice   │
              └──────┬───────┘             └───────────────┘
                     │
                     ▼
              ┌──────────────┐
              │ Twilio       │
              │ Messages API │
              └──────┬───────┘
                     │
                     ▼
              📱 SMS DELIVERY ATTEMPT
```

---

## 🔄 End-to-End Flow

### 1. Outbound Call

The **Start Call** manual trigger starts the workflow, which sends a POST request to the Twilio Calls API using HTTP Basic Authentication.

### 2. IVR Prompt

When the call is answered, Twilio requests the n8n webhook. n8n returns TwiML containing:

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

### 3. DTMF Processing

The `Twilio Digits Webhook` receives the keypad input. The **Pressed 1?** IF node checks whether:

```text
Digits == "1"
```

### 4. Successful Voice Confirmation

When the recipient presses **1**, the workflow returns:

> **“Thank you. Your confirmation has been received. Goodbye.”**

This is the successful voice-confirmation result demonstrated in the assessment recording.

### 5. SMS Integration

After the confirmation response, the workflow triggers the **Twilio Messages API** request for an SMS confirmation.

The SMS node is implemented in the workflow, but **the SMS was not received during the Twilio Trial-account test**. Therefore, this repository does **not** claim successful SMS delivery.

---

## 🧩 Workflow Components

| Node | Responsibility |
|---|---|
| **Start Call** | Starts the test workflow |
| **Initiate Twilio Call** | Creates the outbound call |
| **Twilio Answer Webhook** | Receives the call callback/request |
| **Return IVR TwiML** | Returns the voice instructions |
| **Twilio Digits Webhook** | Receives DTMF and call metadata |
| **Pressed 1?** | Checks the received digit |
| **Respond Confirmed** | Plays the confirmation + goodbye message |
| **Send Confirmation SMS** | Triggers the Twilio Messages API |
| **Respond Not Confirmed** | Handles non-confirmation input |

---

## 📞 TwiML Concepts Used

| Element | Purpose |
|---|---|
| `<Response>` | Root container for Twilio voice instructions |
| `<Say>` | Converts text into speech |
| `<Gather>` | Collects DTMF keypad input |
| `action` | Defines the webhook that receives gathered input |
| `method="POST"` | Sends gathered data through HTTP POST |

The workflow also uses n8n **Respond to Webhook** nodes to return the TwiML responses.

---

## 🔐 Credentials & Security

The Twilio API requests use **HTTP Basic Authentication** with credentials stored in n8n.

### Credentials required

- Twilio Account SID
- Twilio Auth Token
- Twilio voice number / approved sender
- n8n webhook endpoints

### Security practices

- Never commit Auth Tokens or API keys.
- Store secrets in n8n Credentials.
- Use HTTPS webhook endpoints.
- Validate `X-Twilio-Signature` for production webhooks.
- Redact sensitive phone/account information from public evidence.
- Avoid publishing authenticated console URLs.

> **No Twilio Auth Token or secret credential is intentionally stored in this repository.**

---

## 🧪 Test Results

| Test | Result |
|---|---|
| Outbound Twilio call | ✅ Successful |
| IVR prompt | ✅ Successful |
| DTMF input collection | ✅ Successful |
| **Press 1** routing | ✅ Successful |
| Voice confirmation | ✅ Successful |
| Goodbye response | ✅ Successful |
| Twilio Messages API integration | ✅ Implemented |
| SMS delivery during Trial testing | ⚠️ Not received |
| n8n execution monitoring | ✅ Available |

### Important distinction

The project demonstrates **SMS API integration**, but the assessment test did **not** result in a received SMS.

This is intentionally documented rather than presenting an unverified SMS delivery as successful.

---

## ⚠️ Trial Account Limitation

The project was tested using a **Twilio Trial account**.

Trial-account restrictions can affect messaging and destination eligibility. As a result, the SMS confirmation was **not received during the test**, even though the workflow contains the Messages API request.

For a production deployment, the messaging configuration, sender eligibility, destination requirements, and account status should be verified before expecting delivery.

---

## 📦 Repository Contents

| File | Description |
|---|---|
| [n8n Workflow JSON](./Twilio%20IVR%20Call%20%26%20SMS%20Confirmation.json) | Exported n8n workflow |
| [Assessment Report PDF](./Twilio_IVR_Call_SMS_Confirmation_Automation_Report.pdf) | Formal assessment documentation |
| [Screen Recording](./Screenrecording_20260918_180732.mp4) | Assessment demonstration recording |
| [README](./README.md) | Project documentation |

---

## ⚙️ Reproduce the Workflow

### Prerequisites

- n8n Cloud or self-hosted n8n
- Twilio account
- Twilio phone number with voice capability
- Appropriate destination eligibility for testing
- Public HTTPS webhook endpoints

### Steps

1. Download the [workflow JSON](./Twilio%20IVR%20Call%20%26%20SMS%20Confirmation.json).
2. Import it into n8n.
3. Create an HTTP Basic Auth credential with your Twilio Account SID and Auth Token.
4. Configure your own recipient and sender values.
5. Verify the webhook URLs.
6. Activate the workflow.
7. Trigger **Start Call**.
8. Answer the call.
9. Press **1**.
10. Verify the voice confirmation.
11. Review the Twilio Messages API execution and Twilio messaging logs.

> For reproduction, use your own credentials and approved test numbers. Do not copy secrets from screenshots or workflow exports.

---

## 🛡️ Production Hardening

Possible production improvements include:

- Twilio `X-Twilio-Signature` validation
- Input validation and sanitization
- Retry and failure handling
- Call status callbacks
- SMS delivery-status callbacks
- Idempotency protection
- Structured logging
- Environment-specific credentials
- Monitoring and alerting
- Explicit SMS sender configuration / Messaging Service

---

## 🎥 Demo

**Primary demonstration:**  
[▶️ Watch the Loom Recording](https://www.loom.com/share/6b9e96648b5d417eb01f4575389f815d)

The recording demonstrates the voice workflow, including the confirmation interaction after pressing **1**.

---

## 👤 Author

### Shaik Mohammad Shaheed

**AI & Automation · n8n · REST APIs · Webhooks · AI Agents · Generative AI**

This project was built as an automation assessment demonstrating programmable voice, IVR interaction, DTMF processing, webhook-based routing, and Twilio SMS API integration.

---

<p align="center">
  <strong>☎️ Voice → 🎛️ DTMF → 🔀 Routing → 🗣️ Confirmation → 📱 SMS API</strong>
</p>
