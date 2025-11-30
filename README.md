WhatsApp SaaS API v2 (Extended)
================================

Features
--------

This backend is a lightweight WhatsApp Cloud API / Business Management API wrapper designed for:

- JWT-based auth (signup / login / /auth/me)
- Global config via env: WHATSAPP_TOKEN, WHATSAPP_PHONE_NUMBER_ID, WHATSAPP_BUSINESS_ID
- Session messages (24h customer-care window)
  - `/api/messages/session/text` – simple text
  - `/api/messages/session/raw` – full raw payload (text, media, interactive)
- Template send
  - `/api/messages/template` – send ANY approved template
  - Supports:
    - Named or positional parameters
    - Media headers (image/video/document)
    - Media-card carousel templates ("type": "carousel" + "cards")
- Template broadcast
  - `/api/broadcasts/template` – loop over numbers and send template
  - `/api/broadcasts` – inspect basic broadcast history in memory
- Template management (Business Management API)
  - `/api/templates` (GET) – list templates (with pagination support)
  - `/api/templates` (POST) – create template (raw payload passthrough)
  - `/api/templates/:id` (GET) – inspect a template by ID (status, quality, etc.)
  - `/api/templates` (DELETE) – delete template by name + language
- Health check
  - `/healthz`

Environment
-----------

Copy `.env.example` to `.env` and set:

- `PORT` – default 8080
- `JWT_SECRET` – any random secret
- `WHATSAPP_TOKEN` – long-lived access token with permission to:
  - send messages from your phone number
  - manage templates on your Business Account
- `WHATSAPP_PHONE_NUMBER_ID` – phone number ID (Cloud API)
- `WHATSAPP_BUSINESS_ID` – WhatsApp Business Account ID

Run locally
-----------

```bash
npm install
npm start
```

Docker / Cloud Run
------------------

```bash
docker build -t whatsapp-saas-api-v2 .
docker run -p 8080:8080 --env-file .env whatsapp-saas-api-v2
```

The Dockerfile is already compatible with Cloud Run (Node 20, `CMD ["node", "index.mjs"]`).

Notes
-----

- This backend uses `fetch` from Node 20+ (global). No extra HTTP client is required.
- All WhatsApp Graph calls are pinned to **v24.0** so that newer features like media-card carousel templates are available.
- Template creation is **raw passthrough**: the UI (like your DMX tester HTML) should construct the JSON exactly as WhatsApp docs specify.
- "Update" of templates is not directly supported by WhatsApp APIs; you usually delete + recreate or edit via WhatsApp Manager UI.
