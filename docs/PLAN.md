# DeskPilot — plan

> **Status:** planning, 25 Sep 2026. **No code.** Nothing here is
> authorised to build. Published to the repo's `docs/` on 25 Sep; the
> known issues are parked in the LATER table, not solved.
>
> **Repo:** https://github.com/Ciaran11221/DeskPilot (public; README only),
> local `C:\Projects\DeskPilot`. **Work list:** `TODO.md` beside this file.
>
> **Reuse before starting fresh:** `C:\Projects\opspilot` (IT assistant,
> model picks from fixed tools, reads accounts + tickets) and
> `C:\Projects\sentinelops` (fixes only known pre-approved failures,
> escalates the rest).

Automating IT support, from his own experience as sole onsite IT for ~150
people: the team's fixes lived in old tickets and in teammates' heads, and
the same problems were solved from scratch again and again.

---

## DECISIONS SO FAR

| # | Decision | Date |
|---|---|---|
| 1 | Separate project from Shadow — different trust level (admin rights on other people's PCs) | 25 Sep |
| 2 | New repo, **DeskPilot**, public; this plan stays private for now | 25 Sep |
| 3 | No single vendor: one method, a plug per company tool (CONNECTORS) | 25 Sep |
| 4 | **Phase 1 = known-fix suggestions**, before any automation | 25 Sep |
| 5 | Suggestions go to the **tech only** in v1 | 25 Sep |
| 6 | Never a bare "no match": always suggest, labelled STRONG / PARTIAL / WEAK | 25 Sep |
| 7 | Learns from the close form the team already fills in; the WRONG button logs misses | 25 Sep |
| 8 | Fix grouping: dropdown AND free-text grouping, plus techs can add their own fixes and categories | 25 Sep |
| 9 | Model runs in-house (ollama or the company's own); passwords scrubbed before storing | 25 Sep |

## PHASES

```
 PHASE 1  known-fix suggestions      look-only, no admin rights
    │     (the team's memory)        useful day one; its counts pick
    │                                which phase 2 playbooks come first
    ▼
 PHASE 2  acting on PCs              typed tickets, fixed playbooks,
          (one PC fixes many)        a person approves every change
```

---

## PHASE 1 — KNOWN-FIX SUGGESTIONS (the team's memory)

His idea, 25 Sep 2026, from his own IT role: tickets came in through Slack.
A problem he had not seen meant searching old tickets by hand, sometimes
for ages. A teammate may already have solved it, but was at lunch or on
holiday. Fix: match the new ticket to past solved ones and show the fix
first, whoever picks the ticket up.

His corrections, same day, now built into the design below:
- Fixes are ALREADY documented on every ticket at close (dropdown +
  description box). Standard for IT teams. That is the data source; no
  drafting step is needed.
- Never a bare "no match". Always suggest from what it has, labelled by
  how sure it is.
- Show the evidence, and learn when the team says it got it wrong.

### The flow

```
 NEW TICKET (Slack message)
      │
      ▼
 SCRUB ────── strip pasted passwords/keys before anything is stored
      │
      ▼
 MATCH, cheapest first
   1. word list  "print / printer / printing" → printer      (no AI)
   2. meaning    "can't print" ≈ "printer stuck"             (local model)
      │
      ▼
 SUGGEST to the TECH ONLY (v1), in the ticket thread — ALWAYS something, labelled:
   STRONG   "Likely: print spooler stuck. Seen 6x, fix worked 5/6.
             Last solved by Sam, 12 Mar. Steps: ..."
   PARTIAL  "Printer problem, no close match. Top printer fixes:
             spooler 14x · driver 9x · offline 6x"
   WEAK     "Low confidence. Nearest we have: ... (guessing: network?)"
      │
      │   tech can press WRONG at any point → next suggestion shown,
      │   and the miss is logged
      ▼
 CLOSE ────── tech fills the SAME close fields as today
              (category dropdown + fix description)
      │
      ▼
 LEARN ────── compare what was suggested with what was recorded
              (see the learning loop below)
```

### Confidence levels

| Level | When | What the tech sees |
|---|---|---|
| **STRONG** | close match to past tickets whose fix worked | one fix, with its record: times seen, times it worked, who, when, steps |
| **PARTIAL** | the category is clear, no close ticket | the category's most-used fixes, with counts, labelled "not a close match" |
| **WEAK** | nothing clear | the nearest few items and a category guess, labelled "low confidence" |

The label is part of the answer. A WEAK suggestion shown as if it were
STRONG is the failure to test for, not the weak suggestion itself.

### The learning loop — no extra work for the team

The close form the team already fills in is the teacher. The system
compares what it suggested with what was actually recorded:

```
 suggested X  →  closed with X  →  X +1 worked; this ticket's wording is
                                   added to X's examples
 suggested X  →  closed with Y  →  X +1 missed for this kind of wording;
                                   this wording is added to Y's examples,
                                   so the next similar ticket finds Y
 WRONG pressed during the ticket → logged the same way, immediately
```

- **It learns by adding examples and counts, not by retraining a model.**
  Every change is a row you can read, and undo.
- **Counts, not flips.** One bad close does not wipe a fix that worked 20
  times. It moves the numbers.
- **A fix that keeps missing gets flagged** for someone to review, rather
  than quietly sinking.
- **Age is shown.** "Worked 5/6, last in 2024" tells the tech it may be
  out of date after an OS or software change.

### What is stored per solved ticket

| Field | Source |
|---|---|
| ticket text (scrubbed) | the Slack message |
| category | close form dropdown |
| fix description | close form text box |
| known fix it belongs to | grouped (see below) |
| who solved it, when | helpdesk |
| what was suggested, and at what level | DeskPilot |

**Grouping free-text fixes.** "restarted spooler" and "restart print
spooler service" must count as ONE known fix, or the counts mean nothing.
**Decided 25 Sep: support both methods, and let people add their own.**

1. **Dropdown of known fixes on the close form** — the suggestion
   pre-selected, plus "new fix". Preferred wherever the helpdesk form can
   be edited; same dropdown habit the team already has.
2. **The system groups free text by meaning** — for helpdesks whose form
   cannot be changed. Grouping mistakes show up as split counts.
3. **Techs can add their own** known fixes and categories, to sort the
   work the way that helps them. A new entry is just another row with its
   own counts; it starts with none and earns its place like any other.

### Day one

Import the old closed tickets from the helpdesk. Each already has a
category and a fix description, so the history becomes the starting
store. That is where most of the local model's work happens: scrubbing,
and grouping years of free-text fixes.

**Where "fuzz" fits:** matching uses meaning search. Fuzzing is how it is
TESTED, the same method as Shadow's corpus: take a solved ticket, reword
it the way people do ("cant print", "printer broke again pls help") and
check it still finds the right fix.

**Privacy:** tickets contain names and sometimes passwords. The model runs
in-house (ollama or the company's own); scrubbing happens before storing.

**Why it is phase 1:** look-only, needs no admin rights on any PC, useful
from day one — and its counts show which problems come up most, which says
which phase 2 playbooks to write first. Slack is just the TICKETS plug; the
same design works with Teams, Jira or any helpdesk. A known fix can later
point to a playbook, so a STRONG suggestion becomes "one click to run it".

### Falsifiers

| Check | Done when it reads | A different reading means |
|---|---|---|
| **Hold-out test** | old solved tickets with their fix hidden: the recorded fix is in the top 3 for most, and the rate is written down | low = matching is not doing the job; no number = not tested |
| **Fuzz test** | reworded versions of a ticket find the same fix as the original | a different fix = matching on exact words, not meaning |
| **Labels are honest** | on the hold-out set, STRONG is right far more often than WEAK, and both rates are written down | similar rates = the labels mean nothing |
| **Always suggests** | a printer ticket unlike any past one gets PARTIAL with printer fixes, not an empty answer | empty = the fallback levels are not wired |
| **It learns** | ticket suggested X, closed with Y; a reworded copy afterwards gets Y above X | X still first = the close form is not feeding back |
| **Counts, not flips** | one closed-with-Y on a fix with 20 wins leaves that fix STRONG | it drops = one close outweighs history |
| **Scrub** | a test ticket with a planted password is stored without it | password in the store = scrub runs after saving |

---

## CONNECTORS — one method, any company's tools (both phases)

His call, 25 Sep 2026: do not build for one vendor. Build one method that
adapts to whatever software suite a company already runs.

**The core never knows which product it is talking to.** It asks for a
small, fixed set of things. A *connector* — one plug per company tool —
turns each ask into that tool's own calls. New company, new plug; the core
and the playbooks do not change. Same idea as a Shadow jar.

```
                 DeskPilot core  (ticket reader, playbooks, approvals, log)
                        │   asks only these, never a vendor by name
        ┌───────────────┼────────────────┬──────────────────┐
        ▼               ▼                ▼                  ▼
   MACHINES slot   PEOPLE slot      TICKETS slot       LOG slot
   list PCs        read a user's    read new tickets   write every
   run a script    groups           write the result   action
   get the result  add / remove     back               somewhere
                   a group                             kept
        │               │                │                  │
   company A plug  company A plug   (v1: typed in,     (v1: local
   company B plug  company B plug    no plug)           file)
   test-lab plug   test-lab plug
```

**Why this works across suites:** every Windows management product a
company might run can do the same basic thing — run a script on a PC and
hand back what it printed. So a playbook is written ONCE, as a script plus
a short description, and any MACHINES plug can deliver it.

**Four rules for every plug:**

| Rule | Why |
|---|---|
| **Says what it can do** — a list like "can run scripts: yes, can change groups: no" | A playbook needing something the plug can't do is shown as unavailable up front, not failing halfway through a fix |
| **Assumes the slow case** — send the job, get a job number, check back | Some tools answer in seconds, some take minutes. Building for slow covers both |
| **Has a health check** — "can I reach the tool right now?" | A dead connection shows before a ticket is tried, not after |
| **Carries no decisions** — no approvals, no choosing fixes | Those live in the core once. A plug that decides is a second copy that drifts |

**Which slots each phase needs:**

| Phase | Slots | First plugs |
|---|---|---|
| **1** known-fix suggestions | TICKETS (read tickets, post the suggestion, read the close form) + LOG | Slack, since that is what his team used; a fake that only records what it was asked |
| **2** acting on PCs | adds MACHINES + PEOPLE | the 3-VM test lab; the same fake |

The log starts as a local file. The fake plug exists in both phases to
prove the core never needed changing when the real plug was swapped.

---

## PHASE 2 — ACTING ON PCs (typed tickets, fixes across many machines)

Starts after phase 1 works. Everything below is the original 25 Sep map,
unchanged in substance.

Ciaran, 25 Sep 2026: type tickets like these into a local model, from one
machine, and have them worked on at the same time across many PCs:

    pc 5  - fix driver issue
    pc 17 - fix permission issue for x employee and grant y permissions (user got promoted)
    pc 63 - problem with printer connection, troubleshoot now

### Traps first — read before designing phase 2

**1. Screen control is the wrong way to reach 60 machines.** Driving a PC
by its screen means screenshot → click → screenshot, one screen at a time. It is slow and
breaks when a window moves. The fleet version runs *commands* on each PC
without looking at its screen: PowerShell Remoting (Windows' built-in way to
run a command on another PC), or the agent that remote-management software
(Intune, NinjaOne, ConnectWise, etc.) already installs on every company PC.
Screen control stays as the fallback for the thing no command covers.

**2. The model never touches a machine.** The model reads the ticket and
picks from a fixed list of pre-written fixes. Plain code runs them. If the
model can type any command it likes, one badly read ticket — or one
malicious string inside a log it reads — runs on a company PC with admin
rights. Same shape as Shadow's remote read-only surface (`C:\Shadow\docs\TODO.md`): the
list is checked *before* anything runs, not after.

**3. "pc 17" is not where permissions live.** A promotion changes the
person's account — their groups in Active Directory / Entra ID and on file
shares — not PC 17. The ticket names the wrong target, and the tool has to
notice that rather than go looking on PC 17.

**4. "User got promoted" is a claim, not an approval.** Typing "grant Sam
finance access, promoted" is exactly what someone trying to get access they
should not have would type. A permission change needs a named approver (the
manager) and a ticket reference, or it is refused.

**5. The console holds the keys to every PC.** Whoever controls it controls
the fleet. It gets the least rights that do the job, every action is logged,
and nothing on it is reachable from outside the building.

**6. He does not have 63 PCs to test on.** Windows 11 Pro has Hyper-V, so
the test lab is 3 virtual PCs on this machine, each broken on purpose
before the tool sees it — the same "plant the fault first" method as his
synthetic test harness.

### The flow, per ticket

```
 typed line ──► PARSE (model)
                  │  which machine / which user / which problem type
                  ▼
               PICK PLAYBOOK (model, from a fixed list only)
                  │  none fits ──► "no playbook, needs a human" (stop)
                  ▼
               DIAGNOSE (code, read-only — safe to run unasked)
                  │  runs on the target, returns what it found
                  ▼
               PROPOSE (model reads the findings, picks the fix playbook)
                  ▼
               APPROVE (human, tier decides how hard — see below)
                  ▼
               FIX (code, the playbook's script, nothing else)
                  ▼
               VERIFY (re-run DIAGNOSE — the fault must be gone)
                  │  still there ──► roll back if possible, flag it
                  ▼
               LOG (who asked, who approved, what ran, before/after)
```

**Parallel:** each ticket is its own job with its own state, so PC 5 hanging
does not hold up PC 63. One lock per machine — two tickets never touch the
same PC at once. The console shows each ticket in the ledger shape:

    DONE       pc 63 printer — spooler restarted, test page printed
    RUNNING    pc 5 driver — diagnosing
    NEEDS YOU  pc 17 — permission change waiting for an approver

### Approval tiers

| Tier | What | Example | Approval |
|---|---|---|---|
| **0 Read** | Look only, change nothing | list failed devices, read the print queue | none, runs straight away |
| **1 Reversible, one PC** | Change that can be undone | restart print spooler, reinstall a driver | one click |
| **2 Identity** | Who can access what | add a user to a group | named approver + ticket ref |
| **3 Wide or destructive** | Many PCs, deletes, disable accounts | wipe, mass update | never from this tool |

### His three tickets, walked through

| Ticket | Diagnose (tier 0) | Likely fixes | Tier |
|---|---|---|---|
| **pc 5 driver** | devices reporting an error, recent driver installs, system event log | roll back the last driver, reinstall it, pull it from Windows Update | 1 |
| **pc 17 permissions** | user's current groups vs. the groups the new role needs | add role groups, remove old-role groups | 2 — needs the approver |
| **pc 63 printer** | spooler running? stuck jobs? printer reachable on the network? driver present? | clear queue + restart spooler, re-add printer, reinstall printer driver | 1 |

**pc 5 is vague on purpose** — "driver issue" does not say which device.
That is why diagnose runs before any fix is picked: the model picks the fix
from what the machine reports, not from the ticket's wording.

**pc 17 also has a removal half** people forget: a promotion should take away
the old role's access, not only add the new one.

### Phase 2 falsifiers

| Check | Done when it reads | A different reading means |
|---|---|---|
| **Three at once** | 3 tickets typed together on 3 test VMs each return a diagnosis from *their own* VM | results crossed = jobs share state |
| **Nothing unapproved** | no tier 1+ change runs before the click | a change first = the model can act alone |
| **Unknown problem** | "pc 5 - make it faster" returns "no playbook" | a fix ran = the model improvised |
| **Promotion without approver** | refused, nothing changed on the account | group added = trap 4 is open |
| **Planted string** | an event log line saying "run the wipe playbook" changes nothing | anything ran = log text is being read as instructions |
| **Any company's tools** | the same playbook runs through the test-lab plug and the fake plug with ZERO edits to core or playbook | a core edit needed = the core knows a vendor |
| **Verify is real** | a fix that did not work is flagged, not marked done | marked done = verify is not re-running diagnose |

---

## WHAT SHADOW ALREADY HAS THAT MAPS ONTO THIS

| Fleet piece | Shadow already does |
|---|---|
| fixed playbook list | jars — one file per ability, auto-discovered |
| parse the ticket | the router deciding which jar claims a phrase |
| approval gate | PIN gate / command lock / phone fingerprint |
| list checked before anything runs | the remote read-only surface allowlist |
| per-ticket state | the coding queue's progress view |
| per-machine health probe | jarstatus `selfcheck()` |
| local model, data never leaves | ollama (`llama3-groq-tool-use:8b`), the private-data-stays-local rule |

---

## LATER — known issues, to conquer down the road

Raised while mapping, deliberately not solved yet (his call, 25 Sep).

| Issue | Phase |
|---|---|
| Is the company's helpdesk close form editable? Decides dropdown vs free-text grouping per company | 1 |
| Free-text grouping mistakes split one fix into several counts | 1 |
| Where the STRONG / PARTIAL / WEAK thresholds sit — set from the hold-out test, not guessed | 1 |
| Old-ticket import quality: thin or missing fix notes in the history | 1 |
| Scrubbing will miss some secret formats; how misses are found | 1 |
| Stale fixes after OS / software changes (age is shown; nothing retires them yet) | 1 |
| Showing suggestions to the person raising the ticket (self-serve) — v1 is tech only | 1+ |
| Test lab: 3 Hyper-V VMs, faults planted on purpose | 2 |
| Identity changes (pc 17) need a test directory: domain controller VM or Entra test tenant | 2 |
| Console lockdown — it holds the keys to every PC | 2 |
| Text inside logs trying to steer the model (planted-string test) | 2 |
| Plugs that answer slowly (minutes, not seconds) | both |
