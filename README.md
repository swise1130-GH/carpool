# Carpool App — Gainesville / Haymarket Work Commute

A lightweight web app that replaces the weekly WhatsApp poll for our carpool group. Built for ~25 associates who commute between Warrenton, Manassas, and Gainesville to a shared office.

---

## What it does

The app solves a specific problem: when 4 people need to coordinate a same-day departure, all 28 people in the WhatsApp group get notified. This app keeps coordination quiet — only the people going in on a given day are surfaced to each other.

**Four tabs:**

- **Today** — Shows who is going in today, their phone number (tap to text), and a single button to open a blank group text to everyone going in. Used for real-time coordination like "can we leave at 2 instead of 4:45?"
- **My week** — Each person selects their name and taps which days they're going in. Saves instantly to a shared Google Sheet visible to the whole group.
- **Poll results** — Live view of everyone's submissions for the week, day by day, with names and vote counts. Works like the WhatsApp poll view but without notifying anyone.
- **Manage** — Admin area for adding, editing, and removing carpoolers. Not needed week to week.

---

## How to use it (for carpoolers)

1. Open the app link on your phone — no download, no account needed
2. On Sunday, go to **My week**, select your name, tap the days you're going in, hit Save
3. Check **Poll results** anytime to see who else is going in and decide your days
4. On the day of, open **Today** to see who's in and tap to text them directly
5. Need to coordinate a departure time change? Hit "Text everyone going in today" and type your message

That's it. You never need to touch Manage unless you're adding a new person or updating your phone number.

---

## Business rules baked into the app

These are intentional decisions made during design — don't change them without understanding why they exist:

**Lot logic:**
- Three departure lots: Warrenton, Manassas, and Gainesville
- Each carpooler has a preferred lot and a secondary lot
- The group needs a minimum of 3 people per car to use the free express lanes — this is the core reason numbers matter when people check the poll

**Weekly poll structure:**
- Monday offers three choices: Full day, AM only, or PM only — because Mondays have the most schedule variation
- Tuesday through Thursday are full day only — departure time negotiation happens via direct text, not the app
- Friday is AM only — it's a wildcard day so people must actively choose it, it is not assumed

**Passenger expectation:**
- Everyone in the group is expected to be able to take at least 2 passengers
- The minimum in the app is set to 2 — this is a group policy, not a technical limitation

**No daily confirmation:**
- People submit once on Sunday and that's it
- They can edit anytime by going back to My week, selecting their name, and re-saving
- There is no daily check-in by design — it was removed to reduce friction

---

## How it's built (for engineers)

**Stack:**
- Single HTML file (`index.html`) — no framework, no build step, no dependencies
- Vanilla JavaScript, CSS custom properties for theming
- Google Apps Script as a lightweight backend (read/write to Google Sheets)
- GitHub Pages for hosting

**Data:**
- **Roster** — stored in each user's browser localStorage under the key `carpool_roster_v1`. This means the roster is currently device-specific. Moving it to a shared Google Sheets tab (alongside the Poll tab) is the recommended next improvement.
- **Poll submissions** — stored in Google Sheets via a deployed Apps Script web app. Each submission is a row with columns: Name, Mon, Tue, Wed, Thu, Fri, Phone, Updated.
- **Local cache** — poll selections are also cached in localStorage under `carpool_poll_v1` so selections persist between sessions on the same device.

**Google Sheets connection:**
- The Apps Script URL is stored as the `SCRIPT` constant at the top of the JavaScript section in `index.html`
- Submissions use a GET request with a URL-encoded payload parameter (CORS workaround for sandboxed environments)
- Reads use a standard GET request that returns `{ data: [[row], [row], ...] }`
- The Apps Script creates a sheet tab called "Poll" automatically on first run

**To update the Apps Script URL:**
Find this line near the top of the `<script>` section in `index.html`:
```
const SCRIPT='https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec';
```
Replace the URL with the new deployment URL. Every new Apps Script deployment generates a new URL — always use "New deployment" not "Manage deployments" when making script changes.

**To make changes and deploy:**
1. Edit `index.html` locally
2. Go to `github.com/swise1130-GH/carpool`
3. Click `index.html` → click the pencil (edit) icon → paste updated code → Commit changes
4. GitHub Pages redeploys automatically within ~60 seconds

**Known limitations and suggested next improvements:**
- Roster is not shared — moving it to Google Sheets would mean one person adds a new carpooler and everyone sees them immediately
- No weekly reset — the Google Sheet accumulates all submissions over time. A weekly clear or date-stamped submissions would improve long-term data hygiene
- No authentication — anyone with the link can submit as any name. Acceptable for a trusted group; would need rethinking for a larger or public deployment
- Group text on iOS opens Messages with addresses pre-filled; behavior may vary on Android

---

## Who built this

Designed and built by Sue Wise with Claude (Anthropic) as an AI implementation project. The product decisions, business rules, and UX choices were made collaboratively — the goal was to learn how to work effectively with AI tools while solving a real problem.

Engineers in the group are welcome to contribute improvements via pull request.

---

## Questions?

Reach out to Scott or open an issue on this repository.
