# FAFO Registry

Machine-readable source of truth for the FAFO conversion project — Classic World
of Darkness rebuilt on the Pathfinder 1e engine.

The design documents live in Google Drive. This repository holds the small,
queryable spine that says **what exists, where it lives, and what is always
true**. Nothing here is prose canon; it is the index and the invariant set.

---

## The three files

| File | Answers |
|---|---|
| `registry/manifest.yaml` | Which documents exist, at what version, with what Drive ID, and which document is master for which mechanic |
| `registry/invariants.yaml` | Rules that are always true. If generated content contradicts a line here, **the content is wrong, not the invariant** |
| `registry/errata.yaml` | Open gaps and unresolved findings, numbered. Resolved items stay in place rather than being deleted |

### Division of authority

This registry governs **versions, Drive IDs, folder locations, and mechanical
invariants**.

The Master Canon Ledger in Drive governs **what was decided and why**.

Where the two disagree on a version or an ID, the registry wins. Where the
registry has no entry at all, the Ledger governs — see *Coverage is partial*
below.

Above both sits the Master Manifesto. Where any document conflicts with a Core
Law, the Core Law governs and the document is in error.

---

## Reading this repository

**Clone it. Do not fetch raw.**

```
git clone https://github.com/dick-g/fafo-registry.git
```

`raw.githubusercontent.com` has served this repository **days stale** — it
returned `meta.version: 0.1` while `main` had been at `0.4` for over a day.
That is not a cache that will expire; treat raw as unusable, not as a slow
signal. A negative claim about `main` must never rest on it.

A rendered blob-page DOM read is a valid cross-check, but it is **lossy**:
accessibility-tree extraction silently drops scalar values and merges adjacent
keys. Use it to confirm a version number. Never author a full replacement from
it.

`api.github.com` is rate-limited without a token and returns 403 from shared
addresses.

### Cite by key, never by filename

```yaml
physical_states:
  version: "1.2"
  drive_id: 1nIcdDu-FQPEzcKnu-XCKBGDAFoi6SucpHrk_Zx3wbu4
  master_of: [healing, aggravated_damage, torpor, staking, final_death]
```

Filenames change on every version bump. Keys do not.

---

## Coverage is partial

`manifest.yaml` carries an `existence_rule` block. It exists because the honest
answer to "does this document exist?" is currently "check twice."

```yaml
existence_rule:
  manifest_is_authoritative_for: [version, drive_id, folder, status]
  manifest_silence_proves_nonexistence: false
  on_silence: "check the Ledger before concluding a document does not exist"
```

**Manifest silence is not evidence of absence.** The block names the documents
known to exist without a key yet. This has bitten the project repeatedly: a
stale secondary index once produced a confident finding that five written
documents had never been written, and acting on it would have created five
duplicates.

The reverse also holds. Three real, locked documents were invisible to the
manifest until v0.8 — and two of them failed their first invariant check the
moment anyone read them. **Manifest coverage, not audit diligence, is what
closes that gap.** An audit scoped by a list will keep missing whatever the
list omits.

---

## Writing to this repository

Version bumps go in `meta.version` inside each file. The filename never changes.

### Commit procedure

1. Read the current file by **cloning**, and author against those exact bytes.
   Never reconstruct a base from memory or from a lossy read.
2. Validate with a real YAML parser before uploading. A file that does not parse
   is worse than one that is out of date.
3. Upload through the repository's **file-upload UI**. Not the web editor — its
   auto-indent silently corrupts YAML.
4. Verify on the **blob view** afterward, not the commit page: line count, byte
   count, and ideally an md5 against what you authored.

Step 4 is not optional ceremony. A partial edit applied on top of a stale base
once silently reverted an entire prior version's additions, and the commit went
green. `invariants.yaml` carries a `supersedes_note` recording that incident.

### One file per commit

If two files change, make two commits. A bad upload can then be reverted
without taking the other file down. Also worth checking after a second upload
that the first file is still intact — an upload replaces whatever is in the
drop zone.

---

## Conventions

- **Numbered items are stable citation targets.** Errata item numbers, ruling
  IDs (CORE-, GR-, WP-, XL-), and document segment numbers are never reused or
  renumbered. A resolved errata item stays in place marked `resolved` rather
  than being deleted, so a citation to "item 3" never silently retargets.
- **Superseded documents are archived, never deleted.** Moving a file in Drive
  does not change its Drive ID, so every existing pointer survives the move.
- **Ledger entries are never overwritten.** The Ledger is a delta record; a
  later cut corrects an earlier claim rather than editing it away, so the
  reasoning behind a superseded decision stays readable.
- **Line Master Ownership (CORE-006).** Where a dedicated subsystem document
  exists, it is master. Other documents point to it rather than restating its
  mechanics. Restatements drift — one such copy silently dropped a requirement
  and two benefits from the rule it was copying.

---

## Repository layout

```
registry/
  manifest.yaml      what exists, where, at what version
  invariants.yaml    what is always true
  errata.yaml        what is still open
```

Nothing else belongs at the repository root. A duplicate `manifest.yaml` landed
there twice during uploads; both times it was byte-identical on arrival and
would have drifted out of sync on the next update, giving two manifests that
disagree about what is current.
