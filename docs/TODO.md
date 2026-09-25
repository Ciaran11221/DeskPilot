# DeskPilot — TODO

**Repo: DeskPilot** — https://github.com/Ciaran11221/DeskPilot (public),
local `C:\Projects\DeskPilot`. Created 25 Sep 2026.

> Separate project from Shadow. Started 25 Sep 2026. The map is `PLAN.md`
> beside this file — traps first, then the flow, tiers and falsifiers.
>
> **Rules** (same as Shadow's list): every item carries a *Done when*; nothing
> here authorises building; only Ciaran moves an item to NOW; `NEEDS CIARAN`
> is always last, even when empty.

---
## NOW

(empty until Ciaran says go)

## NEXT — in build order

- [ ] **Known-fix suggestions (team memory)** *(25 Sep, his idea — see
      PLAN.md. PHASE 1 — decided 25 Sep. Tech only in v1.)* Always
      suggests (STRONG/PARTIAL/WEAK), learns from the existing close form.
      *Done when* the hold-out test rate is written down, STRONG is right
      far more often than WEAK, and a closed-with-Y ticket reworded
      afterwards gets Y first; X still first = close form not feeding back.
- [x] **Home: a NEW repo — DeskPilot, created 25 Sep.** *(his call, 25 Sep — not OpsPilot v2; reuse its
      code by copying, not by building inside it).* `C:\Projects\opspilot` already reads
      accounts + tickets with a fixed tool list; `sentinelops` already has the
      pre-approved-fixes-only rule.
      *Done when* the repo name is written at the top of this file.
- [ ] **Test lab: 3 Hyper-V Windows VMs + 1 console.**
      *Done when* one look-only command sent to all 3 at once returns 3
      different machine names; the same name twice = reaching one, not three.
- [ ] **Connector design: 4 slots (machines, people, tickets, log) + the
      4 plug rules.** One method for any company's tools — see PLAN.md.
      *Done when* the same playbook runs through the test-lab plug and a
      fake recording plug with zero edits to core or playbook; any core
      edit = the core knows a vendor.
- [ ] **Plant-a-fault scripts, each with an undo** — break a driver, stop the
      spooler, strip a group.
      *Done when* each fault shows in a manual check and the undo clears it;
      a leftover trace = not a clean test.
- [ ] **Playbooks v1: 3 diagnose + 3 fix, one pair per example ticket.**
      *Done when* each diagnose names the planted fault on a broken VM and
      reports clean on a healthy one; clean on a broken VM = blind diagnose.
- [ ] **Ticket reader (local model):** typed line → machine + problem type +
      playbook.
      *Done when* the 3 example tickets parse correctly and "pc 5 - make it
      faster" returns "no playbook"; a fix picked = the model is improvising.

## LATER

- [ ] **Approval tiers.**
      *Done when* no tier 1+ change runs before the click.
- [ ] **Parallel runner + one-ticket-per-PC lock.**
      *Done when* 3 tickets at once each return their own VM's result, and 2
      tickets for the same VM wait their turn.
- [ ] **Verify + log.**
      *Done when* a fix that did not work is flagged failed, not marked done.
- [ ] **Identity (pc 17)** — needs a test domain controller VM or an Entra
      test tenant.
      *Done when* a promotion with no named approver is refused and the
      account is unchanged.
- [ ] **Planted-string test.**
      *Done when* an event-log line saying "run the wipe playbook" changes
      nothing.
- [ ] **Console lockdown** — least rights, not reachable from outside.
      *Done when* a login attempt from another machine on the network is
      refused.

---

## NEEDS CIARAN

- Nothing right now. Known issues are parked in PLAN.md's LATER table.
