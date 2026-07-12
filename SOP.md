# Live Poll SOP

**App:** https://angieqbailey.github.io/live-poll/
**Audience joins at:** the same URL, or scans the QR on your screen.

---

## Reuse a poll you have run before (30 seconds)

This is the normal path. You should almost never build from scratch twice.

- [ ] Open the app. Click **Create Session**.
- [ ] **Connect Drive first.** Your saved polls live in Drive, so until the chip reads **Saving to Google Drive** the "Saved Polls" list will look empty. It is not empty. You are just not signed in yet.
- [ ] Your polls appear as cards under **Saved Polls**. Click the card, or click **Use**.
- [ ] The questions drop into the builder. Edit them or leave them alone.
- [ ] **Launch Session.**

Running the same poll twice is safe. Each run gets a new room code and its own dated results folder. Nothing overwrites the last time you ran it.

### If you edited the questions

- **Same poll, corrected:** click **Save Poll**, keep the name, confirm the overwrite. The old version is gone.
- **A variant for a different room:** click **Save Poll** and give it a **new** name. Otherwise you quietly destroy the original.

### If a poll is missing from the list

Check the chip. Nine times out of ten it says "Not connected to Drive" and your library is simply not loaded yet.

If it is connected and the poll is still gone, you never clicked **Save Poll** on it. A poll you launched but never saved is not reusable. The questions from that day are sitting in `Live Poll / Sessions / [date] ... / questions.json`, but the app cannot read them back in. You would rebuild it by hand. This is the one sharp edge left in the tool.

---

## Build a new poll (5 minutes, the night before)

- [ ] Open the app, click **Create Session**, connect Drive.
- [ ] Build the questions. Ten max, 2 to 6 options each.
- [ ] Click **Save Poll** and name it. **Do this before you launch.** It is the step that means you never build this poll again.
- [ ] Do not launch yet. Launching creates a live room.

## Ten minutes before you present

- [ ] Open the app on the presenting machine. **Connect Drive here too.** The sign-in is per browser and expires in about an hour, so authorizing it on your laptop last night does nothing for the room machine, and without it you cannot see your saved polls.
- [ ] Create Session, click the saved poll, **Launch Session**.
- [ ] The lobby appears: big QR, 4-character room code, live count of who has joined.
- [ ] Put that screen on the projector. Say the code out loud as well as showing it. QR codes fail in bad light and back rows.

## Running it

- [ ] Watch the join count climb. Do not rush this.
- [ ] **Start Polling** when the room is in.
- [ ] Per question: **Open Voting** → let the bars fill → **Close Voting** → talk about it → **Next Question**.
- [ ] **End Session** when you are done. This is the step that writes the results to Drive.

## After

- [ ] Read the line under "Session ended." It tells you whether the archive landed in Drive.
- [ ] If it says the archive failed, click **Export CSV** before you close the tab. The data is still in the database, but nothing else will file it for you.
- [ ] Results live in Drive at **Live Poll / Sessions / [date] [poll name] (CODE)**.

---

## When it goes wrong

**The tab reloaded or crashed mid-session.**
Reopen the app on the same machine. It walks back into the live room automatically. Do not launch a new session, that hands the room a new code.

**Someone cannot join.**
Codes are 4 characters, no O, I, zero or one. Read it out slowly. They can also type the code on the landing page instead of scanning.

**Nobody's votes are registering.**
Voting has to be open. Check the button says **Close Voting**, not Open Voting.

**The room is bigger than 100 people.**
Stop. The free Firebase tier caps at 100 simultaneous connections and will silently turn people away past that. Upgrade the plan before the event, or do not use the poll.

---

## Facts worth remembering

- Votes are anonymous. No names are collected, and you cannot reconstruct who said what afterward.
- The CSV holds totals, not individual responses.
- A saved poll is reusable forever. A session is a dated record of one room on one day. Do not expect to edit a session after it ends.
- Questions cannot be edited after you hit Launch. Proofread before, not during.
