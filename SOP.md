# Live Poll SOP

**App:** https://angieqbailey.github.io/live-poll/
**Audience joins at:** the same URL, or scans the QR on your screen.

---

## The night before (5 minutes)

- [ ] Open the app. Click **Create Session**.
- [ ] Check the chip next to "Saved Polls." If it does not say **Saving to Google Drive**, click **Connect Drive** and sign in. First time on a new machine you will see an "unverified app" warning. Click **Advanced**, then **Continue**. It is your own app.
- [ ] Load a saved poll, or build a new one. Ten questions max, 2 to 6 options each.
- [ ] Click **Save Poll** and name it. This is the step that keeps you from rebuilding it next time.
- [ ] Do not launch yet. Launching creates a live room.

## Ten minutes before you present

- [ ] Open the app on the presenting machine. Reconnect Drive if the chip is not green. The Drive sign-in expires roughly every hour, so do this on the machine you will actually present from, not the one you built the poll on.
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
