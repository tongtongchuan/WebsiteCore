# Triage Labels

The skills speak in terms of five canonical triage roles. This file maps those roles to the actual label strings used in this repo's issue tracker.

| Label in mattpocock/skills | Label in our tracker | Meaning                                  |
| -------------------------- | -------------------- | ---------------------------------------- |
| `needs-triage`             | `needs-triage`       | Maintainer needs to evaluate this issue  |
| `needs-info`               | `needs-info`         | Waiting on reporter for more information |
| `ready-for-agent`          | `ready-for-agent`    | Fully specified, ready for an AFK agent  |
| `ready-for-human`          | `ready-for-human`    | Requires human implementation            |
| `wontfix`                  | `wontfix`            | Will not be actioned                     |

When a skill mentions a role (e.g. "apply the AFK-ready triage label"), use the corresponding label string from this table.

Edit the right-hand column to match whatever vocabulary you actually use.

## Category roles

Triage pairs one category with one state. This repo has no local vocabulary for categories, so the category is carried by whichever surface the issue lives on:

| Category      | GitHub label  | Local markdown field |
| ------------- | ------------- | -------------------- |
| `bug`         | `bug`         | `Category: bug`      |
| `enhancement` | `enhancement` | `Category: enhancement` |

On GitHub the labels are the ones GitHub ships by default. Locally, the field sits next to `Status:` near the top of the issue file.

Issues published to both surfaces carry both. A ticket that is neither a defect nor a new capability (a recurring task, for example) may have no category — don't force one.

## Where state is recorded

This repo has two issue surfaces; the same five roles apply to both.

- **Local markdown** (`.scratch/<feature>/issues/NN-<slug>.md`): a `Status: <role>` line near the top. See `issue-tracker.md` for the file conventions.
- **GitHub** (`BZYA-Community/WebsiteCore`): the label itself.

A `Status: resolved` or `Status: claimed` line is **not** a triage role — those belong to the wayfinder workflow (`issue-tracker.md`), which uses `claimed` / `resolved` for its own tickets. Triage states and wayfinder states are different vocabularies; don't mix them on one ticket.

When a ticket is done or rejected, record *which* it was:

- `resolved` — the work shipped. Cite the commit, PR, or issue that landed it.
- `wontfix` — the request was declined, superseded, or its premise no longer holds. Say which, so a later reader can tell "we decided not to" from "this was fixed".
