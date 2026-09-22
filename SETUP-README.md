# BHS Bites — Login, Accounts & Admin Dashboard

What's new in this update, and what you need to do in the Firebase console to turn it on.

## What was added
- **Log in / Sign up** — a modal (styled to match the site) added to the header and the "My Account" / checkout flow. Email + password, open signup. No changes were made to the site's design, links, or existing features.
- **Real accounts** — student name, student ID, grade, email, wallet balance and points now live in Firestore instead of the browser's local storage. Balance and points update live across devices.
- **Checkout gating** — browsing the menu stays public. Logging in is only required to add funds, place a pre-order, or view "My Account".
- **`admin.html`** — a new admin dashboard page, linked from the header (only visible when logged in as the admin account). Shows:
  - Total revenue, total cost, total profit, orders placed, students, and outstanding wallet balances
  - A students table: student ID, name, email, grade, balance, points, order count, total spent
  - A full order log: date, student ID/name, items, revenue, cost, profit, and an editable status
- **Admin order notifications** — every order creates a protected notification record in the same Firestore transaction. The admin dashboard shows new orders in real time and can display a desktop notification while it is open.
- **Account repair** — if the Auth account exists but its Firestore profile was never created, the next login creates the missing wallet/profile automatically. This also creates the admin profile for `omrsiuomar@gmail.com`.
- **Working demo funds** — demo balance updates now use an atomic Firestore transaction, so the wallet and top-up log stay in sync.
- **`firestore.rules`** — security rules so only `omrsiuomar@gmail.com` can read everyone's data; everyone else can only see and write their own account and orders.

## Cost / profit numbers
You said you don't have real per-item costs yet, so every item's cost is estimated as **55% of its selling price** (a 45% margin) — see the `ITEM_COST_RATIO` constant near the top of the cart script in `index.html`. Update that one number (or replace it with a real per-item cost list) whenever you have actual numbers, and all future orders will use it. Past orders keep whatever cost was recorded at the time.

## What you need to do in the Firebase console
1. **Authentication → Sign-in method** → make sure **Email/Password** is enabled.
2. **Firestore Database** → if you created it in *production mode*, go to **Rules** and paste in the contents of `firestore.rules` from this folder, then click **Publish**. (If you created it in *test mode*, replace those rules with this file too — test mode rules expire and are wide open to anyone.)
3. **Sign in to the admin account first.** Use `omrsiuomar@gmail.com` in the site or `admin.html`. If the Auth account already exists, logging in automatically creates or repairs its Firestore `users/{uid}` profile with a zero wallet and the admin role. If it does not exist yet, create it through the normal signup form.
4. **Authentication → Settings → Authorized domains** — make sure the domain you deploy the site to (e.g. `yourschool.github.io`, a Firebase Hosting domain, etc.) is in the list, or login will fail with an `auth/unauthorized-domain` error.

## Two honest limitations worth knowing about
- **"Add funds" is still a demo.** There's no real payment processor wired in — tapping "Add to balance" increases the Firestore balance and writes a top-up log atomically. A technically-inclined student could still inflate their own demo balance from the browser. Real payments require a payment gateway or a server-side payment verification flow.
- **Same limitation applies loosely to order costs/profits** — they're computed and stored by the browser at checkout using the estimated margin above, so treat the admin dashboard's profit numbers as an estimate until you plug in real item costs.

## Files in this folder
- `index.html` — the site, with login/signup + real accounts wired in
- `admin.html` — the admin dashboard (new)
- `firestore.rules` — paste into the Firebase console (see step 2 above)
- `install.html`, `manifest.webmanifest`, icons — unchanged; `sw.js` has a bumped cache version so the fixes are picked up
