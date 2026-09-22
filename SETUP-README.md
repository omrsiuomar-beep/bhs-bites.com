# BHS Bites — Login, Accounts & Admin Dashboard

What's new in this update, and what you need to do in the Firebase console to turn it on.

## What was added
- **Log in / Sign up** — a modal (styled to match the site) added to the header and the "My Account" / checkout flow. Email + password, open signup. No changes were made to the site's design, links, or existing features.
- **Real accounts** — student name, student ID, grade, email, wallet balance and points now live in Firestore instead of the browser's local storage. Balance and points update live across devices.
- **Checkout gating** — browsing the menu stays public. Logging in is only required to add funds, place a pre-order, or view "My Account" (as you asked for).
- **`admin.html`** — a new admin dashboard page, linked from the header (only visible when logged in as the admin account). Shows:
  - Total revenue, total cost, total profit, orders placed, students, and outstanding wallet balances
  - A students table: student ID, name, email, grade, balance, points, order count, total spent
  - A full order log: date, student ID/name, items, revenue, cost, profit, and an editable status
- **`firestore.rules`** — security rules so only `omrsiuomar@gmail.com` can read everyone's data; everyone else can only see and write their own account and orders.

## Cost / profit numbers
You said you don't have real per-item costs yet, so every item's cost is estimated as **55% of its selling price** (a 45% margin) — see the `ITEM_COST_RATIO` constant near the top of the cart script in `index.html`. Update that one number (or replace it with a real per-item cost list) whenever you have actual numbers, and all future orders will use it. Past orders keep whatever cost was recorded at the time.

## What you need to do in the Firebase console
1. **Authentication → Sign-in method** → make sure **Email/Password** is enabled.
2. **Firestore Database** → if you created it in *production mode*, go to **Rules** and paste in the contents of `firestore.rules` from this folder, then click **Publish**. (If you created it in *test mode*, replace those rules with this file too — test mode rules expire and are wide open to anyone.)
3. **Claim the admin account first.** Before you share the site publicly, open the site yourself and sign up using `omrsiuomar@gmail.com` as the email. That's what grants admin access — the Firestore rules only trust that exact email address, not any name or setting inside the app, so nobody else can grant themselves admin by tampering with the browser.
4. **Authentication → Settings → Authorized domains** — make sure the domain you deploy the site to (e.g. `yourschool.github.io`, a Firebase Hosting domain, etc.) is in the list, or login will fail with an `auth/unauthorized-domain` error.

## Two honest limitations worth knowing about
- **"Add funds" is still a simulation.** There's no real payment processor wired in — tapping "Add to balance" just increases the Firestore balance directly, the same way the old local-storage version did. That's fine for a prototype, but a technically-inclined student could in principle inflate their own balance by calling the app's code directly from the browser console. Closing that gap fully requires a real payment gateway (or at least a Cloud Function that verifies payments before writing balance), which needs Firebase's paid Blaze plan — happy to help set that up when you're ready to take real payments.
- **Same limitation applies loosely to order costs/profits** — they're computed and stored by the browser at checkout using the estimated margin above, so treat the admin dashboard's profit numbers as an estimate until you plug in real item costs.

## Files in this folder
- `index.html` — the site, with login/signup + real accounts wired in
- `admin.html` — the admin dashboard (new)
- `firestore.rules` — paste into the Firebase console (see step 2 above)
- `install.html`, `sw.js`, `manifest.webmanifest`, icons — unchanged
