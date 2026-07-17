# Flask Login App (SQLite)

A simple, working authentication app: register, log in, view a dashboard, and log out.
Passwords are hashed with Werkzeug's `generate_password_hash` — never stored in plain text.

## Structure

```
flask_login_app/
├── app.py              # Flask app: routes, DB setup, auth logic
├── requirements.txt
├── static/
│   └── style.css
└── templates/
    ├── base.html
    ├── login.html
    ├── register.html
    └── dashboard.html
```

## Setup

```bash
cd flask_login_app
python3 -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate
pip install -r requirements.txt
python3 app.py
```

The app creates `users.db` (SQLite) automatically on first run and starts at
`http://127.0.0.1:5000`.

## How it works

- **`users` table**: `id`, `username` (unique), `email` (unique), `password_hash`.
- **Register** (`/register`): validates input, hashes the password, inserts the row.
  Duplicate username/email is caught via SQLite's `UNIQUE` constraint.
- **Login** (`/login`): looks up the user, checks the password hash, stores
  `user_id` in the signed session cookie.
- **Dashboard** (`/dashboard`): protected by a `login_required` decorator that
  redirects to `/login` if there's no session.
- **Logout** (`/logout`): clears the session.

## Before deploying anywhere real

- Replace `app.secret_key` in `app.py` with a long random value (e.g. `python3 -c "import secrets; print(secrets.token_hex(32))"`), and load it from an environment variable instead of hardcoding it.
- Run with `debug=False` in production, behind a real WSGI server (gunicorn, waitress, etc.).
- Consider adding rate limiting on `/login` to slow down brute-force attempts.
- Consider HTTPS-only cookies (`SESSION_COOKIE_SECURE = True`) once served over HTTPS.
