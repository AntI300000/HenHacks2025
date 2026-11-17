# PassWarrior

A password-security checker built at **HenHacks 2025** (University of Delaware). Enter a password and PassWarrior shows how long it would take to crack, whether it has appeared in known data breaches, and AI-generated suggestions for making it stronger.

## How it works

A Django backend exposes one JSON endpoint (`POST /API/`). Two clients call it: a web page and a native iPhone app.

```
Web client (HTML/CSS/JS) ─┐
                          ├─► Django API ─┬─► zxcvbn             (crack-time estimate)
iOS client (SwiftUI) ─────┘               ├─► Have I Been Pwned  (breach check)
                                          └─► Google Gemini      (improvement advice)
```

- **Crack time:** [zxcvbn](https://github.com/dwolfhawk/zxcvbn-python) estimates time to crack against offline slow hashing.
- **Breach check:** uses the [Pwned Passwords range API](https://haveibeenpwned.com/API/v3#PwnedPasswords) with k-anonymity. The password is SHA-1 hashed and only the first 5 characters of the hash are sent, so the password itself never leaves the server.
- **AI advice:** Gemini 1.5 Flash, authenticated with OAuth 2.0, returns suggestions that are converted from Markdown to HTML for display.

## Tech stack

| Layer | Technologies |
|---|---|
| Backend | Python, Django |
| Web client | HTML, CSS, JavaScript |
| iOS client | Swift, SwiftUI |
| External services | Have I Been Pwned, Google Gemini API |

## Running locally

Requires Python 3.10+.

```bash
cd backend
pip install -r requirements.txt
cp EXAMPLE.env .env          # then fill in DJANGO_SECRET_KEY
cd mysite
python manage.py runserver
```

Open http://127.0.0.1:8000/ for the web client.

**Gemini setup (optional):** place a Google OAuth client secret file (`client_secret_*.json`) in `backend/`. The first request opens a browser window to authorize, and the token is cached in `token.json`. Without it, the crack-time and breach checks still work.

**iOS client:** open `app/PassWarrior/PassWarrior.xcodeproj` in Xcode and run on a simulator. It calls the backend at `127.0.0.1:8000`, so start the server first.

## Known limitations

This is a hackathon prototype, not a production service.

- The Gemini feature sends the password in the prompt. A safer design would send only the zxcvbn analysis.
- Both clients point to a hardcoded local address, and the Django settings are development defaults (`DEBUG = True`).

## Team

| Member | Contributions |
|---|---|
| [@AntI300000](https://github.com/AntI300000) (Anthony Imperiale) | Django backend and JSON API, zxcvbn integration, API restructuring, deployment prep |
| [@jonesrmj](https://github.com/jonesrmj) (Ryan Jones) | SwiftUI iOS app, Have I Been Pwned and Gemini API integrations |
| [@danengl06](https://github.com/danengl06) (Danial England) | Web client (HTML/CSS/JavaScript) |
