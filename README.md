# WOYZ Academy

A single-file HTML learning platform for the "AI for Indian Doctors And Clinics (AIDC)" course — login, dashboard, and a course player with Vimeo video support.

Everything lives in `index.html`. No build step, no dependencies to install — just deploy the file.

## 1. Deploy

**GitHub Pages**
1. Push this repo to GitHub.
2. Go to **Settings → Pages** → set source to the `main` branch, root folder.
3. Your site will be live at `https://<your-username>.github.io/<repo-name>/`.

**Netlify**
Drag the folder into [app.netlify.com/drop](https://app.netlify.com/drop), or connect the GitHub repo for auto-deploys on push.

## 2. Connect Firebase

Open `index.html` and find the `firebaseConfig` object near the top of the `<script type="module">` block. Replace the `REPLACE_ME` placeholders with your Firebase project's web app config (Firebase console → Project settings → your app → SDK setup and configuration).

Until you do this, the app still works fully in **local demo mode** — click "Continue with demo account" on login. Progress and videos save to the browser's localStorage instead of Firestore.

Firestore collections used once connected:
- `progress/{uid}` — per-user lesson completion
- `videoMap/shared` — the Vimeo video IDs assigned to each lesson (shared across all users, since it's course content, not personal data)

Make sure Firestore is in production or test mode with rules that allow authenticated reads/writes to these paths.

## 3. Add videos, one at a time

Open a course inside the app, click **Edit videos** in the top bar, and paste a Vimeo video ID or URL into any lesson. Hit **Save** — that lesson switches to the real embedded player immediately for everyone.

To make videos badge-free by default on Vimeo's side: create an embed preset (Appearance → Embed on any video → Save new preset) and set it as your account default under **Account settings → Videos → Embed presets**.

## 4. Course content

The curriculum lives in the `sampleCourses` array in the script. To edit lesson titles, durations, or add a new section, edit that array directly — or migrate it to a Firestore `courses` collection if you want to edit content without touching code.

## Notes

- No authentication permission checks yet — anyone signed in can see "Edit videos." Fine for solo use; ask if you want it locked to a specific admin email before sharing access.
- The favicon/manifest is a minimal inline PWA manifest so it can be added to a phone's home screen.
