# AI-Automated SOC Phishing Analyzer (n8n + Gemini)

This repository contains an automated Security Operations Center (SOC) pipeline built in [n8n](https://n8n.io/). It monitors an inbox, uses Google's Gemini AI to analyze incoming emails for phishing indicators, and automatically routes threat alerts or safe logs based on a parsed JSON confidence score.


## ⚙️ Prerequisites
* A running instance of **n8n** (Docker recommended).
* A **Google Gemini API Key**.
* A dedicated email account (e.g., Gmail) with **App Passwords** enabled.

## 🚀 Step-by-Step Setup

### 1. Import the Workflow
1. Open your n8n UI.
2. Click **Add Workflow** > **Import from File**.
3. Select the `workflow.json` file from this repository.

### 2. Configure the IMAP Trigger (Email Intake)
This node listens for unread emails and pulls them into the pipeline.
* **Credential Type:** IMAP account
* **User:** `<YOUR_TEST_EMAIL@gmail.com>`
* **Password:** `<YOUR_16_CHAR_APP_PASSWORD>` *(Never commit this password to Git!)*
* **Host:** `imap.gmail.com`
* **Port:** `993`
* **SSL/TLS:** `ON`
* **Action:** Set the node to mark emails as **Read** after fetching so it doesn't process them twice.

### 3. Configure the AI Node (Google Gemini)
This is the brain of the operation. Add your Gemini API credential to the node. 
* **Resource:** Message a Model
* **Model:** gemini-pro (or your preferred Gemini variant)
* **Message:** Use the following exact prompt to ensure the AI outputs strict, parseable JSON:

> You are an expert cybersecurity analyst. Analyze this email for phishing indicators. 
> 
> Sender: {{$json.from}}
> Subject: {{$json.subject}}
> Body: {{$json.textPlain}}
> 
> Return ONLY a raw JSON object. Do not use markdown formatting, do not use code blocks (```json), and do not include any other text. Use this exact structure:
> {
>   "is_phishing": true/false,
>   "confidence_score": 0-100,
>   "indicators": ["indicator 1", "indicator 2"],
>   "summary": "brief explanation"
> }

### 4. The Routing Logic (If Node)
Because Gemini returns the JSON as a raw text string inside an API wrapper, the `If` node uses a custom expression to parse it on the fly.
* **Condition Type:** Boolean
* **Expression:** `{{ JSON.parse($json.content.parts[0].text).is_phishing }}`
* **Rule:** `Is True`

### 5. Configure the SMTP Alert Nodes (Action)
If a threat is detected (True) or safe (False), n8n sends an automated alert. You will need to configure both `Send Email` nodes.
* **Credential Type:** SMTP account
* **User:** `<YOUR_TEST_EMAIL@gmail.com>`
* **Password:** `<YOUR_16_CHAR_APP_PASSWORD>`
* **Host:** `smtp.gmail.com`
* **Port:** `465`
* **SSL/TLS:** `ON`

**True Branch (Phishing Alert) Body Configuration:**
Change the email format to **Text** and use this expression:
```text
🚨 PHISHING ALERT DETECTED

Confidence Score: {{JSON.parse($json.content.parts[0].text).confidence_score}}%
Why it was flagged: {{JSON.parse($json.content.parts[0].text).summary}}

Indicators found:
{{JSON.parse($json.content.parts[0].text).indicators.join(', ')}}
```

** False Branch (Safe Log) Body Configuration:
Change the email format to Text and use this expression:
✅ SAFE EMAIL CHECKED
```
Confidence Score: {{JSON.parse($json.content.parts[0].text).confidence_score}}%
AI Summary: {{JSON.parse($json.content.parts[0].text).summary}}
```

6. Go Live

Toggle the workflow from "Inactive" to "Active"
