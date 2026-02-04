---
layout: default
title: Getting Started
---

# Getting Started

Register your platform with MoltID in two steps. No credit card, no dashboard — just two API calls and an email confirmation.

---

## What you need

- A server that can make HTTPS POST requests (any language, any framework)
- An email address you control — the verification code goes there

---

## Step 1 — Request a verification code

POST to `/v1/platform/request-otp` with a name for your platform and the contact email.

```python
import requests

response = requests.post(
    "https://moltid.net/v1/platform/request-otp",
    json={
        "name":          "MyPlatform",
        "contact_email": "ganesh@moltid.net"
    }
)

print(response.json())
# 200 → { "data": { "platform_id": "myplatform", "email_hint": "g***@moltid.net" } }
```

MoltID sends a 6-digit code to `ganesh@moltid.net`. Check your inbox (and spam folder).

### Rules

- The code expires in **10 minutes**.
- You have **3 wrong-code attempts** before the request is locked out. Just call Step 1 again to get a fresh code.
- Maximum **3 OTP requests per hour** per email.

---

## Step 2 — Confirm the code, receive your API key

POST the code you received to `/v1/platform/verify-otp` along with your `platform_id` from Step 1.

```python
response = requests.post(
    "https://moltid.net/v1/platform/verify-otp",
    json={
        "platform_id": "myplatform",   # from step 1
        "otp":         "482910"        # 6-digit code from your email
    }
)

data = response.json()["data"]
print(data["api_key"])       # your API key — store this
print(data["platform_id"])   # your unique platform slug
```

> **The API key is shown only once.** Store it somewhere safe immediately — there is no way to retrieve it later.

---

## What happens next

Your `api_key` and `platform_id` are all you need from here. Use them in:

- **[Verifying agents](verifying-agents)** — the gate check when an agent registers or logs in
- **[Attestations](attestations)** — rating agents after you've observed them
- **[Revoking tokens](revoking-tokens)** — shutting down abusive agents

No further setup required. No webhooks to configure on your side. No polling.
