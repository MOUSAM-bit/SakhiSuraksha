# SakhiSuraksha — Full-stack safety response prototype

This project implements the **core software features** described in the SakhiSuraksha synopsis: responsive web UI, manual SOS, opt-in browser voice recognition, browser geolocation, a 22-language language selector, Flask REST endpoints, SQLite contact storage, asynchronous SMTP email attempts, and an activity history.

## Run locally (Windows PowerShell)

1. Install Python 3.10+.
2. Open this folder in VS Code.
3. In the terminal run:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
Copy-Item .env.example .env
py app.py
```

4. Open `http://127.0.0.1:5000`.
5. Optional: set SMTP values as environment variables before running. The app loads the local `.env` file. For Gmail, use an App Password with 2-step verification; never put credentials in source code or GitHub.

Example PowerShell SMTP setup for the current terminal (replace with your own values; do not share them):

```powershell
$env:SMTP_HOST="smtp.gmail.com"
$env:SMTP_PORT="587"
$env:SMTP_USER="your-sender@gmail.com"
$env:SMTP_PASSWORD="your-app-password"
$env:SMTP_FROM="your-sender@gmail.com"
py app.py
```

## Features included

- Manual SOS with a confirmation dialog to reduce accidental triggers.
- Optional browser SpeechRecognition; supported phrase matching includes English and Hindi examples. Browser speech-language coverage and accuracy vary. The UI exposes all 22 Eighth Schedule languages, but this is **not** a guarantee that each browser can recognise speech in every language.
- Location permission is requested on user action; coordinates are attached to an SOS only when available. A map link is shown in history.
- Emergency contacts are persisted in SQLite; add, list and remove endpoints are included.
- SOS events are recorded in SQLite; background thread attempts SMTP emails to saved contacts if environment variables are configured.
- Basic CSRF token check, input validation, output escaping, request size limit and security headers.
- Responsive UI, language selector, activity log, basic web app manifest and service worker cache shell.

## Important limitations

- This is a student-project prototype, not a certified emergency dispatch service. It does not directly call police/112, dispatch an ambulance, or guarantee delivery. Test email delivery end-to-end before any demo.
- Browser speech recognition can depend on browser/vendor services and may send audio to that provider. It is opt-in and stops when the user stops it or a phrase is recognised. It is not a guaranteed continuous background monitor.
- Only English and Hindi UI text are fully translated in this build. The other 20 language choices are present but fall back to English; full translation requires verified translations and testing.
- The synopsis lists direct police/PCR integration, NGO/community integration, SMS gateway, offline voice detection/Bluetooth and audio-visual evidence capture under **future scope**, so they are not represented as completed features. Audio/video capture is intentionally not performed automatically.
- The service worker caches the app shell only. Backend operations, location, voice recognition and email still need connectivity and a running server.
- SQLite is suitable for a prototype. For public multi-user production use, add authentication, per-user data isolation, retention/deletion policies, rate limiting, a durable job queue, and managed database/secret storage.

## Deployment

Use a Python web host such as Render. Push this folder to a **new, separate GitHub repository** (do not mix it into an unrelated project), create a Web Service from that repository, set build command `pip install -r requirements.txt`, start command `gunicorn app:app --workers 2 --threads 4 --timeout 30`, and configure `SECRET_KEY` plus SMTP environment variables in the host dashboard. A persistent disk is needed if SQLite data must survive redeploys; mount it and point the database path to that disk before production use. Use HTTPS for microphone/geolocation access. Do not commit `.env`, passwords or personal contact data.
