# FIDO Login

This is a Flask web application that demonstrates **FIDO2 / WebAuthn (passkeys)** login.
Users can:
1) Register an account (email + password)
2) Register a passkey (Windows Hello / security key / phone)
3) Log in using the passkey (passwordless or as a second step depending on the flow)

---

## How the application works

### Step 1 — User registers (account creation)
- The user enters email + display name + password.
- The app stores the user record in the database (`user` table), including a password hash.
- At this stage, the user may not have any passkey yet.

### Step 2 — User enrolls a passkey (WebAuthn registration)
- The user clicks “Register Passkey / FIDO” in the UI.
- The server generates WebAuthn **registration options** (challenge + RP info + user info).
- The browser calls `navigator.credentials.create()` and prompts the user:
  - Windows Hello (face/pin), security key, or phone (depending on browser/device/settings)
- The browser returns an **attestation response** to the server.
- The server validates the response and stores the credential fields on the user record, such as:
  - `credential_id`
  - `public_key`
  - `sign_count`
  - `authenticator_id`
  - `user_verified`
  - `attestation_format`

### Step 3 — User logs in using the passkey (WebAuthn authentication)
- The user clicks “Login with Passkey / FIDO”.
- The server generates WebAuthn **authentication options** (challenge + allowed credential IDs).
- The browser calls `navigator.credentials.get()` and prompts the user again.
- The browser returns an **assertion** response.
- The server verifies:
  - challenge matches
  - origin + RP ID match
  - signature is valid using the stored `public_key`
  - sign counter is consistent
- If valid, the user is logged in.

---

## Requirements
- Python 3.11 recommended
- Browser: Chrome/Edge
- For Windows Hello face/pin: Windows Hello set up in Windows settings

---

## Project structure
- `web/` – Flask application
  - `fido_app/` – main app package
  - `wsgi.py` – starts the server
  - `setup-db.py` – initializes database tables
- `docker-compose.yml` – optional Docker setup

---

## Setup & run (local development)

### 1) Clone the repo and go into the web folder
```bash
git clone https://github.com/MclK02/mclk02-fido-login
cd FIDO-login/web
