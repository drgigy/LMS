# WOYZ Academy

A single-file HTML learning platform for the "AI for Indian Doctors And Clinics (AIDC)" course — email login/sign-up, dashboard search, subscription-ready course locking, course player, lesson notes, progress tracking, completion certificate, and Vimeo video support.

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

Until you do this, the app still works fully in **local demo mode** — click "Continue with demo account" on login. Demo access unlocks the course locally, and progress, notes, and videos save to the browser's localStorage instead of Firestore.

Firestore collections used once connected:
- `progress/{uid}` — per-user lesson completion
- `enrollments/{uid_courseId}` — course access records created by your payment webhook or admin process
- `videoMap/{courseId}` — Vimeo video IDs assigned to lessons for one course

Use `firestore.rules` as the starting point for production rules. It allows learners to read only their own progress/enrollment documents and lets enrolled learners read video IDs only for courses they can access. Do not let the browser create paid enrollments directly; create them from a trusted backend after payment confirmation.

## 3. Subscription access

Each course can define a `price`, `accessLabel`, and `description` inside `sampleCourses`. If a signed-in learner does not have an active enrollment document, the dashboard shows the course as locked and the course page shows a subscription panel.

Expected enrollment document path:

```text
enrollments/{uid}_{courseId}
```

Example:

```json
{
  "uid": "USER_UID",
  "courseId": "aidc",
  "status": "active",
  "source": "stripe",
  "accessUntil": "2027-07-07"
}
```

The checkout button is intentionally disabled until you connect a backend. Update `checkoutConfig` in `index.html` after creating a Stripe/Razorpay checkout Cloud Function. Your payment webhook should write the enrollment document only after payment succeeds.

Admin access in the included rules expects a Firebase Auth custom claim:

```json
{ "admin": true }
```

## 4. Add videos, one at a time

Open a course inside the app, click **Edit videos** in the top bar, and paste a Vimeo video ID or URL into any lesson. Hit **Save** — that lesson switches to the real embedded player immediately for everyone.

To make videos badge-free by default on Vimeo's side: create an embed preset (Appearance → Embed on any video → Save new preset) and set it as your account default under **Account settings → Videos → Embed presets**.

## 5. Course content

The curriculum lives in the `sampleCourses` array in the script. To edit lesson titles, durations, or add a new section, edit that array directly — or migrate it to a Firestore `courses` collection if you want to edit content without touching code.

## 6. Learner features

- Use the dashboard search icon to find a course, section, or lesson and jump directly into the player.
- Use Previous, Next, and Mark complete inside the course player to move through the course.
- Lesson notes are private to the current browser and save automatically.
- The Certificate tab unlocks printing once every lesson in the course is complete.

## Notes

- Add your admin email to `adminEmails` in `index.html` before sharing the site. Only listed admin emails see the video editing controls.
- The favicon/manifest is a minimal inline PWA manifest so it can be added to a phone's home screen.
