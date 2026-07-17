# SmartRoad Admin Panel — Project Brief for Claude Code

## Project
Web-based admin panel for SmartRoad. Reads/writes the same Firestore database as the Android app — no separate backend server.

## Tech Stack
- Plain HTML/CSS/JavaScript, no build tooling, no npm required
- Firebase JS SDK (modular v12, via CDN with `<script type="module">` and `import` statements)
- The Firebase config/initialization block below is included as an inline `<script type="module">` directly in every HTML page that needs Firebase (Login, Dashboard, Manage Reports, Report Details) — not extracted into a shared file. Each page adds whatever additional Firebase imports it needs alongside this same base block (e.g. `getAuth` + `signInWithEmailAndPassword` for the login page; `getFirestore` + `collection`/`getDocs`/`doc`/`updateDoc`/`deleteDoc` for pages that read/write report data)
- Run locally via any static server (e.g. VS Code Live Server extension, or `python -m http.server`) — no deployment needed for this assignment. Note: ES modules require serving over HTTP(S), not opening the HTML file directly via `file://`

## Firebase Web Config (include this exact block, inline, in every page)
```html
<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/12.16.0/firebase-app.js";
  import { getAnalytics } from "https://www.gstatic.com/firebasejs/12.16.0/firebase-analytics.js";

  const firebaseConfig = {
    apiKey: "AIzaSyA3akQlH2taxlk1QspxIapL40MarzEroAA",
    authDomain: "smartroad-ac252.firebaseapp.com",
    projectId: "smartroad-ac252",
    storageBucket: "smartroad-ac252.firebasestorage.app",
    messagingSenderId: "146408848910",
    appId: "1:146408848910:web:f42852b785b3485da1865f",
    measurementId: "G-5R5V5V9CTL"
  };

  const app = initializeApp(firebaseConfig);
  const analytics = getAnalytics(app);
  // Additional Firebase service imports/initialization (Auth, Firestore) added per page as needed
</script>
```
Like `google-services.json` on the mobile side, this config is not a true secret and is fine to commit to GitHub — actual security is enforced by Firestore Security Rules, not by hiding this file.

## Admin Identification
An account is an admin if its `users/{uid}` Firestore document has `isAdmin: true`. After login, check this field — if false/missing, show an error and sign the user out.

**Current status: Firestore is still in open test-mode rules during development** — the `isAdmin` check above is currently a front-end UX gate only, not real enforcement. Proper Firestore Security Rules (restricting update/delete on `hazard_reports` to admins only) are intentionally deferred until the final polish phase, right before submission — see the rules already drafted in the project setup conversation. **Do not skip this step before final submission.**

## Data Model (shared with the Android app — do not change field names)

### `users` collection (doc ID = Firebase Auth UID)
| Field | Type |
|---|---|
| fullName | string |
| username | string |
| email | string |
| createdAt | timestamp |
| totalReports | number |
| reportsResolved | number |
| isAdmin | boolean (only present/true on admin accounts) |

### `hazard_reports` collection (doc ID = auto-generated)
| Field | Type |
|---|---|
| userId | string |
| username | string |
| hazardType | string — `Pothole`, `Flood`, `Accident`, `Fallen Tree`, `Traffic Light` |
| description | string |
| photoUrl | string (Cloudinary URL) |
| latitude | number |
| longitude | number |
| status | string — `New`, `Under Investigation`, `Resolved` |
| userAgent | string |
| createdAt | timestamp |

## Pages

1. **Admin Login** — email/password via Firebase Auth. On success, verify `isAdmin` field; if not true, show an error and sign out immediately.
2. **Dashboard** — live counts: Total Users, Total Reports, Open Reports (status != Resolved), Resolved Reports. Below that, a "Recent Reports" table (latest ~10): username, hazard type, date, status.
3. **Manage Reports** — search box (by username or description text), filter controls (hazard type, date range, status), full table of `hazard_reports` with View / Update Status / Delete actions per row.
4. **Report Details** — full record: username, hazard type, description, coordinates, date/time, user agent, photo. Editable status dropdown (New / Under Investigation / Resolved) + Save button (updates Firestore). Delete button (with confirmation).

## Rubric-Critical Requirements
- [ ] Full CRUD on hazard reports from the admin side (Read, Update status, Delete — Create happens from the mobile app)
- [ ] Report list/table displays: username, date and time, user-agent, hazard type, description, photo, GPS coordinates
- [ ] Search and filter by hazard type, date, and status
- [ ] Photo viewing works correctly (Cloudinary URLs load directly in `<img>` tags)
- [ ] Only authenticated admin can access the panel and make changes
- [ ] **Before final submission:** deploy real Firestore Security Rules (drafted earlier — restrict `hazard_reports` update/delete to admins, keep test-mode rules only during active development)
