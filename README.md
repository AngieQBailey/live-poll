# Live Poll

Real-time audience polling for workshops and talks. The presenter builds questions, the audience joins with a 4-character code or a QR scan, and everyone watches the room decide on their own phone.

Live at **https://angieqbailey.github.io/live-poll/**

---

## Two doors

- **Audience:** `/` — a join box. Nothing else. No way into the builder.
- **Presenter:** `/#present` — a Google sign-in, then the builder.

Only `angiebaileycomo@gmail.com` can create a session, and that is enforced by the database rules, not by hiding the button. Anyone can find `#present` in the page source; it will not help them.

The presenter sign-in carries the `drive.file` scope, so authorising the presenter and connecting Drive are one action.

## Console state this build expects

All of this is already done on project `live-poll-33579`. Recorded here so it can be rebuilt.

1. **Firebase → Authentication → Sign-in method:** **Anonymous** enabled (the audience) and **Google** enabled (the presenter). Both are required. Anonymous alone breaks presenting; Google alone breaks voting.
1b. **Firebase → Authentication → Settings → Authorized domains:** `angieqbailey.github.io` added. Firebase keeps its own domain allowlist for sign-in popups, **separate** from the Authorized JavaScript origins in Google Cloud. Setting the Cloud origin is not enough. Miss this and the presenter sign-in fails with `auth/unauthorized-domain`.
2. **Firebase → Realtime Database → Rules:** the contents of `database.rules.json`, published.
3. **Google Cloud → Drive API:** enabled, OAuth consent screen in Testing, `drive.file` scope, Angie as the sole test user, web client restricted to the `angieqbailey.github.io` origin.

---

## What this is

One file. `index.html` holds the markup, the CSS and the JavaScript. There is no build step, no framework, no package.json. Edit the file, commit, GitHub Pages serves it.

The only backend is a Firebase Realtime Database (project `live-poll-33579`). Sessions, votes and participants live there. Nothing else runs on a server.

Google Drive holds the reusable polls and the results archive. It is authorised by the presenter's sign-in, not as a separate step.

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

1. Open `/#present` and sign in with Google. This unlocks the builder and connects Drive in one action.
2. Pick a saved poll, or build questions: multiple choice (2 to 6 options) or a 1 to 5 rating scale with your own end labels. Ten questions max.
3. **Save Poll** to give it a name you will recognise later. Launching files it either way, so an unsaved poll can no longer be lost. **Launch Session** to go live.
4. The QR screen shows the code, a live count of who has joined, and how many have already answered question 1. **Question 1 is open from the moment you launch**, so people can answer as they arrive. **Show Results** moves your own screen to the tally; it does not gate the room.
5. Per question: watch the bars fill, **Close Voting** to lock it, **Next Question**. Voting opens automatically on every question.
6. On the last question, **Show Final Results** puts the whole poll on every screen.
7. **End Session**. Results are archived to Drive automatically if Drive is connected. **Export CSV** gives you a local copy either way.

**Audience**

Scan the QR, or open the link, or type the code on the landing page. No name, no signup, no app. One vote per question per browser.

They see results only after they answer: the live tally for the question they just voted on, then the whole poll once the presenter shows the final results. Someone who skips a question sees nothing for it.

The final screen is one verdict card per question, not a wall of bars: short label, winning answer, a single segmented bar, and whether they were with the room. Full breakdown behind a tap. A question can carry an optional `short` label for this screen, set in the builder; without one the full question text is used. The presenter keeps the detailed view.

### If the presenter tab reloads or crashes

Recovery is automatic. The room code is written to `localStorage`, and on load the app checks whether that session is still alive and walks back into it. Only the browser that created the session can drive it. The banner across the top offers a way out if you want to abandon the room and start fresh. The record expires after 12 hours.

Audience devices do **not** store a resume record. Their way back in is the `#join/CODE` hash, which joining writes into the URL, so a refresh rejoins on its own. (A stored record used to silently rejoin a stale room while someone was typing a fresh code.)

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

### Setup (already done, recorded for a rebuild)

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
7. Commit and push. Open `/#present` and sign in with Google. The same sign-in grants Drive; there is no separate connect step.

Any polls already saved in a browser are lifted into Drive on first sign-in. Nothing is lost.

The Drive token lasts about an hour and is held in `localStorage`, so a new tab keeps it. (It used to live in `sessionStorage`, which is per-tab: a second tab looked signed in while quietly having no Drive, and the results archive would fail.) A new day needs a new sign-in. `Reconnect Drive` in the builder runs the same Google popup.

---

## Firebase security rules

`database.rules.json` in this repo is the rule set. It is **documentation of what should be live**, not something that deploys itself. Paste it into the Firebase console: **Realtime Database → Rules → publish**.

What the rules enforce:

- **Only the presenter creates.** A session can be written only by a signed-in user whose verified email is `angiebaileycomo@gmail.com`. An anonymous visitor who finds `#present` and pokes at the database gets `PERMISSION_DENIED`.
- **No bulk reads.** `/sessions` cannot be listed. You have to know a room code to read a room.
- **Sign-in required.** The audience is signed in anonymously on load. There is no login screen; they never see it.
- **Only the presenter drives.** The session records the creator's `uid` as `owner`. Only that account can change `status` or `currentQ`.
- **Questions are frozen at launch.** They can be written once, at creation, and never edited afterward.
- **One vote per person per question**, keyed by auth uid, and **only while voting is open**. Closing voting is enforced by the database, not by hiding the buttons.
- **A vote must be for the question that is live.** You cannot back-fill a question the presenter already closed and discussed, and you cannot vote ahead. Without this, a phone asleep on an old ballot could change a number the room had already been told.

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

Status runs: `voting ⇄ closed → voting → … → summary → ended`. Terminal at `ended`.

A room is `voting` from the moment it launches, so the audience can answer question 1 with no presenter action. `summary` means polling is over and every screen is showing the whole poll; voting is refused in that state. The presenter's own view of "lobby versus live" is a client-side flag, not a database status.

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

**2026-07-12 — v2.1**

- Launching a poll files it into the Polls library automatically. Previously a poll launched without hitting Save Poll was unrecoverable: its questions went into the session archive, which the builder cannot read back. Auto-filing never overwrites an existing entry (an edited version of a saved poll lands as `[name] (edited [date])`) and is capped at 6 seconds so a slow Drive cannot stall a launch with a room already waiting.

**2026-07-12 — v3.3**

- The room is votable from launch. No Open Voting step, on any question.
- Voters see the live tally for a question once they answer it, own answer marked, and nothing at all if they sat the question out.
- **Show Final Results** on the last question puts the whole poll on every screen until the session ends.
- Votes must belong to the question that is currently live.
- No fake local identity when sign-in is slow. A device that cannot be identified is stopped at the door rather than shown a ballot it cannot submit.
- Verified end to end, including the Drive archive.

**2026-07-12 — v3**

- Presenter is a gated Google sign-in at `#present`. The landing page is audience-only.
- Database rules refuse to create a session for any account but the presenter's. Hiding the builder is now backed by enforcement.
- The presenter sign-in carries the Drive scope, so "Connect Drive" is no longer a separate step.
- Launching a poll files it to the library automatically. An unsaved poll can no longer be lost.
- Headline is now "What's the room think?"

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
