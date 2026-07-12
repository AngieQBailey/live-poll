# Live Poll SOP

**Your door (bookmark this):** https://angieqbailey.github.io/live-poll/#present
**The audience's door:** https://angieqbailey.github.io/live-poll/ or the QR on your screen.

Those are two different doors on purpose. The audience page has no way into the builder. Yours needs a Google sign-in, and only your account can create a poll.

---

## Reuse a poll you have run before (30 seconds)

This is the normal path. You should almost never build the same poll twice.

- [ ] Open **/#present**. Click **Sign in with Google**.
- [ ] Your polls appear as cards under **Saved Polls**. Click the card, or click **Use**.
- [ ] The questions drop into the builder. Edit them or leave them alone.
- [ ] **Launch Session.**

The sign-in also connects Drive. There is no separate "Connect Drive" step any more. One sign-in, both jobs.

Running the same poll twice is safe. Each run gets a new room code and its own dated results folder. Nothing overwrites the last time you ran it.

### If you edited the questions

Do nothing. Launch. The edit is filed for you as `[name] (edited [date])` and **the original is never touched.** You end up with both.

The only way to destroy the old version is to click **Save Poll**, keep the same name, and confirm the overwrite. That takes a deliberate yes, so you will not do it by accident.

### If a poll is missing from the list

You are almost certainly not signed in. Saved polls live in Drive, so before you sign in the list is empty. It is not lost, it is not loaded.

You also cannot lose a poll by forgetting to save it. **Launching a poll files it automatically.** Look for it under the name you gave it, or under `Untitled Poll [date] [time]` if you never named it.

Save Poll still matters for one thing: giving it a name you will recognise in six months.

---

## Build a new poll (5 minutes, the night before)

- [ ] Open **/#present**, sign in.
- [ ] Build the questions. Ten max, 2 to 6 options each.
- [ ] Click **Save Poll** and name it. Launching would file it for you anyway, but then it lands under a date-stamped name you will not recognise later. Name it now.
- [ ] Do not launch yet. Launching creates a live room.

## Ten minutes before you present

- [ ] Open **/#present** on the machine you will actually present from and **sign in there.** The sign-in is per browser and expires in about an hour. Signing in on your laptop last night does nothing for the room machine, and until you do it here you cannot see your saved polls.
- [ ] Click the saved poll, **Launch Session**.
- [ ] The QR screen appears: big QR, 4-character room code, live count of who has joined, and a line telling you how many have already answered.
- [ ] Put that screen on the projector. Say the code out loud as well as showing it. QR codes fail in bad light and back rows.

**Question 1 is live the moment you launch.** Anyone who scans can read it and answer while you are still talking. You do not have to press anything to let them in.

## Running it

- [ ] Let them arrive and answer. The QR screen tells you how many have.
- [ ] **Show Results** when you are ready to talk about it. This moves your screen only. It does not gate the room.
- [ ] Per question: watch the bars fill → **Close Voting** when you want to lock it → talk about it → **Next Question**.
- [ ] **Next Question opens voting for that question automatically.** There is no Open Voting step to forget.
- [ ] **End Session** when you are done. This is the step that writes the results to Drive.

## After

- [ ] Read the line under "Session ended." It tells you whether the archive landed in Drive.
- [ ] If it says the archive failed, click **Export CSV** before you close the tab. The data is still in the database, but nothing else will file it for you.
- [ ] Results live in Drive at **Live Poll / Sessions / [date] [poll name] (CODE)**.

---

## When it goes wrong

**The tab reloaded or crashed mid-session.**
Reopen the app on the same machine. It walks back into the live room automatically. Do not launch a new session, that hands the room a new code.

**You are signed in but Launch Session does nothing.**
Your Drive sign-in expired. Sign out and back in. Nothing is lost.

**Someone cannot join.**
Codes are 4 characters, no O, I, zero or one. Read it out slowly. They can also type the code on the landing page instead of scanning.

**Nobody's votes are registering.**
Voting opens by itself on every question, so this should not happen. If it does, check the button says **Close Voting** (voting is open) rather than **Open Voting** (you closed it, probably by accident).

**The room is bigger than 100 people.**
Stop. The free Firebase tier caps at 100 simultaneous connections and will silently turn people away past that. Upgrade the plan before the event, or do not use the poll.

**Someone else needs to run a poll.**
They cannot. Only your Google account can create a session, enforced by the database, not by the interface. Adding a second presenter is a small change, but it is a change. Ask for it before the day, not during.

---

## Facts worth remembering

- Votes are anonymous. No names are collected, and you cannot reconstruct who said what afterward.
- The CSV holds totals, not individual responses.
- A saved poll is reusable forever. A session is a dated record of one room on one day. Do not expect to edit a session after it ends.
- Questions cannot be edited after you hit Launch. Proofread before, not during.
- Launching opens the room. If you launch an hour early, people can answer question 1 an hour early. Launch when you are ready for them.
- Signing in is what unlocks the builder AND Drive. If something feels broken, check that first.
