# SmartRoad Admin

Web-based administrator panel for the SmartRoad crowdsourced road hazard reporting system.

Reads and writes the same Firestore database used by the SmartRoad Android app — no separate backend server. Administrators can review, filter, update, and manage all hazard reports submitted by users.

## Features

- Admin login via Firebase Authentication, gated by an `isAdmin` flag on the account
- **Dashboard:** live counts (total users, total reports, open reports, resolved reports), plus a recent reports table
- **Manage Reports:** searchable, filterable table of all hazard reports (by hazard type, date, and status), with view, update status, and delete actions
- **Report Details:** full record view — username, hazard type, description, GPS coordinates, date/time, user-agent, and photo — with an editable status and save/delete controls

## Tech stack

- Plain HTML, CSS, and JavaScript — no build tooling or npm required
- Firebase JS SDK (modular, loaded via CDN)
- Cloud Firestore as the shared database with the mobile app

## Setup

1. Clone this repository
2. Serve the folder locally with any static server, for example:
   ```
   python -m http.server
   ```
   or use VS Code's Live Server extension.

   > ES modules require the site to be served over `http://` — opening `index.html` directly (`file://`) will not work.
3. Open the served URL in your browser
4. Log in with an admin account — this requires a Firestore `users/{uid}` document with `isAdmin: true` for that account's UID (see the main SmartRoad repo's setup notes)

## Security

Firestore Security Rules restrict updating and deleting hazard reports to authenticated admin accounts only. Regular users (via the mobile app) can create and read reports, but cannot modify or delete them.

## Related repository

Android mobile app (report hazards, view live map): [SmartRoad](https://github.com/norirfan10/SmartRoad)

## Developed by

- Nor Irfan Bin Nor Hisham
- Danial Farhan Bin Muhammad Faisal
- Muhammad Eidlan Muqri Bin Munir

*ICT602 Mobile Technology Group Project*

## License

© 2026 SmartRoad Team. All rights reserved.
