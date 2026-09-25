# DeskPilot

Type IT support tickets in plain English and have them worked on across
many PCs at the same time, from one machine.

    pc 5  - fix driver issue
    pc 17 - user promoted, update their access
    pc 63 - printer not connecting, troubleshoot

**Status: planning. No code yet.**

## How it will work

- **A local AI model reads each ticket** and picks from a fixed list of
  pre-written fixes. It cannot run a command itself, and company data never
  leaves the building.
- **Every ticket starts with a look-only check** on the target PC. The fix is
  chosen from what the PC reports, not from the ticket's wording.
- **Nothing that changes a PC runs until a person approves it.** Access
  changes also need a named approver; "user got promoted" on its own is
  refused.
- **After a fix, the check runs again.** If the problem is still there, the
  ticket is marked failed, not done.
- **Every ticket is logged:** who asked, who approved, what ran, before and
  after.
- **A ticket with no matching fix goes to a human.** The model does not
  improvise one.

## Approval levels

| Level | Example | Approval |
|---|---|---|
| Look only | read the print queue, list devices with errors | none |
| Can be undone, one PC | restart the print spooler, reinstall a driver | one click |
| Who can access what | add a user to a group | named approver + ticket reference |
| Many PCs, or deletes | wipe, mass update | never from this tool |

## Testing

To be tested against 3 Windows virtual machines, each broken on purpose
before DeskPilot sees it — a known fault in, the fix checked on the way out.

## Related

- [OpsPilot](https://github.com/Ciaran11221/opspilot) — IT assistant that
  reads accounts and tickets with a fixed set of tools. Read-only.
- [SentinelOps](https://github.com/Ciaran11221/SentinelOps) — fixes only
  known, pre-approved failures and hands everything else to a person.

DeskPilot is the next step: the same fixed-list rule, allowed to act on real
PCs, one approval at a time.

## License

MIT
