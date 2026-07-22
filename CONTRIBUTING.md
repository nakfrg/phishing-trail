# Contributing events to The Phishing Trail

All game content lives in `data/` as plain JSON — you never need to touch
`index.html` to add or edit an event.

| File | What's in it |
|---|---|
| `data/events.json` | The main pool of random security events (the fun part — add yours here) |
| `data/openers.json` | Monday-morning opening events (one is picked at random per run) |
| `data/specials.json` | Scripted beats: the ransomware attack, the Friday 4:59 PM finale, the fell-asleep event |
| `data/peace.json` | Short "nothing happened" filler moments between events |
| `data/mails.json` | Emails for the PHISH-or-LEGIT inbox training minigame |

## Quick start: add an event

Append an object to the array in `data/events.json`:

```json
{
  "id": "myevent",
  "title": "SOMETHING HAPPENS",
  "icon": "mail",
  "text": "What the player sees. HTML is allowed: <span class=\"dim\">dim text</span> and <br> line breaks.",
  "hint": "The IT ADMIN's insider knowledge about this event (shown when they spend an IT INSTINCT charge).",
  "choices": [
    { "label": "THE RISKY OPTION",
      "go": { "oops": 1, "d": { "sec": -15 },
        "msg": "What happened because of the choice.",
        "tip": "The real-world security lesson. This is the educational payload — make it true." } },
    { "label": "THE SMART OPTION",
      "go": { "smart": 1, "d": { "sec": 10, "rep": 4 },
        "msg": "What happened.",
        "tip": "The lesson." } }
  ]
}
```

Rules of thumb:
- `id` must be unique (each event fires at most once per run).
- 2–4 choices. Labels are UPPERCASE, short, and funny.
- Every outcome should teach something real; every disaster should be based on a real attack.
- Validate your JSON before committing (`python3 -m json.tool data/events.json` or any JSON linter).
- Test by running a local server (see README) — the game loads your event into the random pool.

## Event fields

| Field | Required | Meaning |
|---|---|---|
| `id` | yes | Unique slug |
| `title` | yes | Panel title (UPPERCASE) |
| `icon` | yes | Pixel icon shown on screen — one of: `mail, usb, phone, wifi, door, db, skull, stone, coffee, qr, gift, badge, printer, laptop, bug, cable, note, trophy, zoom, clock` |
| `text` | yes | Event description (string, or variant list — see below) |
| `hint` | no | IT ADMIN's IT INSTINCT hint |
| `choices` | yes | Array of `{ label, go }` |
| `roles` | no | Limit the event to certain roles, e.g. `["ceo"]` or `["intern","manager","itadmin"]`. Roles: `intern`, `manager`, `itadmin`, `ceo` |
| `cond` | no | Only fire if a flag is set: `{ "anyFlag": ["credsLeaked", "postedQuiz"] }` |
| `extra` | no | Conditional text appended after `text`: `[{ "ifFlag": "oooLeak", "t": " …extra sentence." }]` |

## Outcomes (`go`)

A simple outcome:

| Field | Meaning |
|---|---|
| `msg` | Result text shown to the player |
| `tip` | "SECURITY TIP" box under the result |
| `d` | Stat changes: `{ "energy": -5, "sec": -20, "rep": 8 }` (any subset; `{}` for none) |
| `smart` / `oops` | `1` to count this as a smart call / questionable click (affects the final rank) |
| `setFlag` / `clearFlag` | Set or clear a story flag (string or array) |
| `death` | Instant game over instead of `msg`/`d`: `{ "cause", "title", "msg", "tip" }`. `cause` completes the epitaph "…who \<cause\>" |
| `endgame` | `true` only on finale choices that end the week in victory |
| `setEnergy` | Force energy to a value (used by the sleep event) |

Damage guide: small mistake `sec -8..-15`, big mistake `-20..-35`, good call `sec +5..+15`.
The engine scales damage by role (interns take less, CEO/IT admin take more), so keep values in base terms.

### Branching outcomes

When one choice can end differently, use `branches` — the first matching branch wins,
and the last branch (no conditions) is the fallback:

```json
"go": { "branches": [
  { "ifFlag": "postedQuiz", "death": { "cause": "…", "title": "…", "msg": "…", "tip": "…" } },
  { "roll": 0.25, "oops": 1, "d": { "sec": -30 }, "msg": "25% of the time…", "tip": "…" },
  { "roll": 0.8,  "oops": 1, "d": { "sec": -10 }, "msg": "next 55% of the time…", "tip": "…" },
  { "oops": 1, "d": { "rep": -8 }, "msg": "the remaining 20%…", "tip": "…" }
] }
```

- `ifFlag` / `ifNotFlag` — take this branch only if the flag is / isn't set.
- `roll` — one random number 0–1 is drawn per choice; a branch is taken if the
  number is below its `roll`. So `0.25` then `0.8` means 25% / 55% / 20% (fallback).

### Flags in current use

`credsLeaked`, `malware`, `breachedAcct`, `intruder`, `postedQuiz`, `oooLeak`,
`unpatched`, `stickyNote`, `adware`. `malware`/`breachedAcct` make the ransomware
event likely — set them when the player installs or approves something bad.
You can invent new flags; unknown flags are simply never set.

### Placeholders

These work in `text`, `hint`, `msg`, `tip`, and `death` fields:

| Placeholder | Becomes |
|---|---|
| `{name}` | Player's name |
| `{NAME}` | Player's name in caps |
| `{boss}` | "the CEO" (or "the board chair" when playing as CEO) |
| `{Boss}` | Capitalized version |
| `{BOSS}` | "CEO" / "BOARD CHAIR" |
| `{BOSS_}` | "CEO" / "BOARD_CHAIR" (underscored) |

## Inbox minigame emails (`data/mails.json`)

```json
{ "from": "sender@domain", "sub": "Subject line", "body": "One or two sentences.",
  "phish": true, "why": "Shown after answering — explain the tells." }
```

Keep a roughly even phish/legit split so the minigame stays fair.
