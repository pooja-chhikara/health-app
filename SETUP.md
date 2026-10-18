# Pip — Setup (Gemini `.env` + Firebase Auth, fully integrated)

This is your full project with everything wired in: Firebase login/signup gating
"Get started" (and "New check-in" / profile chip / "View all" history), and Gemini
reading its key from `.env` instead of the old profile-settings field.

## 1. Install dependencies

```bash
npm install
```

(`firebase` is already added to `package.json`.)

## 2. Set up your `.env`

1. Rename `env.example.txt` in this project to `.env` (exact name, leading dot, no `.txt`).
2. Fill in your real values:
   - `VITE_GEMINI_API_KEY` — from [Google AI Studio](https://aistudio.google.com/apikey)
   - `VITE_FIREBASE_*` — from Firebase Console → Project settings → your web app's config
3. `.env` is already in `.gitignore` — never commit it.

## 3. Enable Email/Password sign-in in Firebase

In the [Firebase Console](https://console.firebase.google.com):
1. Select your project → **Build → Authentication → Sign-in method**.
2. Enable **Email/Password**.

## 4. Run it

```bash
npm run dev
```

## Log in, sign up and log out (latest update)

- **Logged out:** the navbar shows **Log in** and **Sign up**. Each opens the same
  floating window, on the matching tab. A link under the form switches between the two.
- **Sign up** asks for name, country, email and password, creates the Firebase account,
  and saves the country to the Realtime Database under `users/{uid}/profile`.
- **Logged in:** the navbar shows the profile chip, **Log out** and **Get started**.
  **Log out** is also inside Profile settings.
- After logging out, a grey "You are logged out" banner shows for 4 seconds and the
  dashboard goes back to empty until someone logs in again.
- Files touched: `src/App.jsx`, `src/components/Navbar.jsx`, `src/components/LoginWindow.jsx`.

## What changed vs. your previous version

- **New:** `src/firebase.js`, `src/utils/auth.js`, `src/components/LoginWindow.jsx`
- **`src/App.jsx`** — now tracks Firebase auth state. "Get started", "Check my health",
  "New check-in", the profile chip, and "View all" history are all login-gated: if
  you're logged out, `LoginWindow` opens first, and the window you actually wanted
  (check-in / history / profile) opens automatically right after you log in or sign up.
  A green "Welcome, Name 👋" banner shows for 4 seconds after login.
- **`src/components/Navbar.jsx`** — shows "Log in" when logged out, or the profile
  chip + "Log out" when logged in.
- **`src/components/ProfileSettings.jsx`** — the Gemini API key/model fields are gone
  (no longer needed); shows your logged-in email with a "Log out" button instead.
- **`src/components/FloatingCheckWindow.jsx`** / **`ChatWidget.jsx`** — no longer take
  a `geminiSettings` prop; they call `askGemini()` / `hasGeminiKey()` from
  `src/utils/gemini.js`, which now reads `VITE_GEMINI_API_KEY` / `VITE_GEMINI_MODEL`
  straight from `.env`.
- **`src/utils/storage.js`** — check-in history and profile are now saved per Firebase
  user (`localStorage` key includes the UID), so different accounts on the same
  browser don't mix data.

## Next: GitHub + Render

Once this runs locally without errors:

```bash
git init
git add .
git commit -m "Add Firebase auth and .env-based Gemini integration"
git branch -M main
git remote add origin <your-github-repo-url>
git push -u origin main
```

Then on [Render](https://render.com): New → Static Site → connect your repo →
Build command `npm run build`, Publish directory `dist` → add the same `VITE_*`
environment variables under Render's Environment settings (Render can't read your
local `.env` file — you re-enter the same key/value pairs there).
