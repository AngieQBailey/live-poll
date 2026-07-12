# Live Poll

Real-time audience polling for workshops and talks. Presenter builds questions, audience joins with a 4-character code or a QR scan, results fill in live on the projected screen.

Live at **https://angieqbailey.github.io/live-poll/**

---

## Console switches this build expects

Two things live outside the repo. The app degrades gracefully without them, but it is not secure until both are done.

1. **Firebase console → Authentication → Sign-in method → enable Anonymous.** Without it, the app falls back to a random local id and the security rules will reject every write once they are published. Do this **before** publishing the rules.
2. **Firebase console → Realtime Database → Rules →** paste `database.rules.json` and publish.

Optional, and separate: [Connecting Google Drive](#connecting-google-drive).

---

## What this is

One file. `index.html` holds the markup, the CSS and the JavaScript. There is no build step, no framework, no package.json. Edit the file, commit, GitHub Pages serves it.

The only backend is a Firebase Realtime Database (project `live-poll-33579`). Sessions, votes and participants live there. Nothing else runs on a server.

Optional: Google Drive, for saving reusable polls and archiving results. The app works without it.

### Dependencies (all CDN, all pinned except fonts)

| What | Where | Version |
|---|---|---|
| Firebase app + auth + database (compat SDK) | gstatic | 9.23.0 |
| qrcodejs | cdnjs | 1.0.0 |
| Google Identity Services | accounts.google.com | rolling |
| Imbue, IBM Plex Sans, IBM Plex Mono | Google Fonts | rolling |

---

## Running a session

**Presenter**

1. Open the site, click **Create Session**.
2. Pick a saved poll, or build questions: multiple choice (2 to 6 options) or a 1 to 5 rating scale with your own end labels. Ten questions max.
3. **Save Poll** if you want it back later. **Launch Session** to go live.
4. The lobby shows a QR code, the 4-character room code and a live count of who has joined. Wait for the count, then **Start Polling**.
5. Per question: **Open Voting**, watch the bars fill, **Close Voting**, **Next Question**.
6. **End Session**. Results are archived to Drive automatically if Drive is connected. **Export CSV** gives you a local copy either way.

**Audience**

Scan the QR, or open the link, or type the code on the landing page. No name, no signup, no app. One vote per question per browser.

### If the presenter tab reloads or crashes

Recovery is automatic. The room code is written to `localStorage`, and on load the app checks whether that session is still alive and walks back into it. Only the browser that created the session can drive it. The banner across the top offers a way out if you want to abandon the room and start fresh. The record expires after 12 hours.

---

## Connecting Google Drive

Until you do this, saved polls live in one browser and die with a cache clear.

Drive uses the `drive.file` scope, which lets the app see **only the files it created itself**. It cannot read the rest of your Drive. The flip side: a poll JSON you create by hand in Drive is invisible to the app.

### Folder layout the app builds and maintains

```
Live Poll/
  Polls/
    Workshop Opener.livepoll.json      reusable poll definitions
  Sessions/
    2026-07-12 Workshop Opener (K7M2)/
      questions.json                   what was asked
      results.csv                      what the room said
```

Polls are editable and reusable. Sessions are dated, immutable records of one room on one day. They stay separate on purpose.

### One-time setup

1. Go to the [Google Cloud Console](https://console.cloud.google.com/) and select project **live-poll-33579** (the Firebase project is already a Cloud project, so use it rather than making a new one).
2. **APIs & Services → Library →** search "Google Drive API" → **Enable**.
3. **APIs & Services → OAuth consent screen**. User type: **External**. Fill in app name and support email. Add scope `https://www.googleapis.com/auth/drive.file`. Add your own Google account under **Test users**.
4. Leave publishing status as **Testing**. You will see an "unverified app" warning when you sign in. Click Advanced → Continue. That is expected and it is only ever you. `drive.file` is a non-sensitive scope, so if the warning gets old you can publish without a security review.
5. **APIs & Services → Credentials → Create Credentials → OAuth client ID**. Application type: **Web application**. Under **Authorized JavaScript origins** add:
   - `https://angieqbailey.github.io`
   - `http://localhost:8000` (only if you test locally)
   No redirect URI is needed.
6. Copy the client ID. In `index.html`, find `GOOGLE_CLIENT_ID` near the top of the script block and paste it in:
   ```js
   const GOOGLE_CLIENT_ID = "182821462877-xxxxxxxx.apps.googleusercontent.com";
   ```
7. Commit and push. Open the site, go to Create Session, click **Connect Drive**, sign in once.

Any polls already saved in that browser are lifted into Drive on first connect. Nothing is lost.

The access token lasts about an hour and is held in `sessionStorage`, so a tab reload will not make you sign in again. A new day will.

---

## Firebase security rules

`database.rules.json` in this repo is the rule set. It is **documentation of what should be live**, not something that deploys itself. Paste it into the Firebase console: **Realtime Database → Rules → publish**.

What the rules enforce:

- **No bulk reads.** `/sessions` cannot be listed. You have to know a room code to read a room. Before this, one request returned every poll ever run.
- **Sign-in required.** The app signs in anonymously on load. There is no login screen; the audience never sees it.
- **Only the presenter drives.** The session records the creator's `uid` as `owner`. Only that account can change `status` or `currentQ`.
- **Questions are frozen at launch.** They can be written once, at creation, and never edited afterward.
- **One vote per person per question**, keyed by auth uid, and **only while voting is open**. Closing voting is now enforced by the database, not by hiding the buttons.

Sessions are never deleted, by design of these rules. Nothing cleans up old rooms.

---

## Data model

```
sessions/
  K7M2/
    code: "K7M2"
    owner: "<presenter auth uid>"
    createdAt: 1751328000000
    currentQ: 2
    status: "lobby" | "waiting" | "voting" | "closed" | "ended"
    questions/
      0: { type: "multiple_choice", text: "...", options: ["A", "B", "C"] }
      1: { type: "rating_scale", text: "...", min: 1, max: 5,
           lowLabel: "Not at all", highLabel: "Completely" }
    votes/
      0/
        <uid>: { value: "A", timestamp: 1751328100000 }
    participants/
      <uid>: { joined: 1751328050000 }
```

Status runs: `lobby → waiting → voting ⇄ closed → waiting → … → ended`. Terminal at `ended`.

Rating scales are hardcoded to 1 through 5. The `min` and `max` fields are written but never read.

---

## Known limits

- **100 simultaneous connections** on the Firebase free tier. A room of 30 is fine. A room of 150 will start refusing people, quietly. Upgrade the plan before a big keynote.
- **Every audience device subscribes to the whole session node.** That means each browser already holds the live tallies even though the UI never shows them. Anyone with devtools open can see the results early. It also means vote traffic fans out to everyone, which is what caps room size in practice.
- **Anonymous is anonymous.** No names are collected anywhere. You cannot tell who voted for what, and neither can anyone else.
- **A second device is a second vote.** Duplicate protection is one vote per signed-in browser. Someone with a phone and a laptop can vote twice. Fine for a workshop, not fine for an election.
- **Nothing is ever deleted.** Old sessions accumulate in the database forever.

---

## Changelog

**2026-07-12 — v2**

- Presenter session survives a page reload. Was: refresh killed the room and stranded the audience.
- Poll library backed by Google Drive, with a results archive per session. Was: `localStorage` only.
- Anonymous auth plus security rules. Was: the database was open to the world.
- All question and option text is escaped before rendering. Was: a script tag in an option ran on the projected screen.
- Room codes claimed by transaction. Was: a duplicate code silently overwrote a live session.
- Votes rejected unless voting is open. Was: enforced only by hiding the buttons.
- Vote listener no longer stacks up on every open/close of voting.
- CSV quotes escaped properly. A question containing a quotation mark used to break the file.
- Lobby QR redraws on resize and rotation.
- The ten-question cap in the copy is now a real cap.

**v1** — original build.
