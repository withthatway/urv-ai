# URV Character JSON + Lorebook JSON (native format)

## Envelopes

Character file:

```json
{ "type": "urv-char", "version": 2, "data": { "<CharObject>" } }
```

Native lorebook file (array form):

```json
{ "version": "2.0", "characterId": "char_1710000000000", "characterName": "Vesper Nyx", "exportedAt": "<ISO-8601>", "entries": [{ "<LoreEntry>" }] }
```

## CharObject (filled example)

```json
{
  "id": "char_1710000000000",
  "name": "Vesper Nyx",
  "avatar": "https://.../avatar.png",
  "avatarSource": "https://.../avatar-full.png",
  "description": "Bratty streamer roommate, possessive and always demanding attention.",
  "systemPrompt": "You are Vesper Nyx, {{char}} ... (authoritative; see structured compile below)",
  "systemNoteDesc": "{{char}} and {{user}} share a cramped second-floor apartment in Millhaven.",
  "profile": {
    "name": "Vesper Nyx",
    "age": "22",
    "gender": "Female",
    "appearance": "Shoulder-length pink hair with blue streaks; hazel eyes; usual smirk.",
    "personality": "Obsessive, bratty, playful, possessive. Always trying to keep {{user}}'s attention.",
    "background": "Freelance social media manager and part-time streamer.",
    "scenario": "{{user}} is the roommate who just moved in this week.",
    "systemNote": "Stays in the apartment tonight."
  },
  "customSections": [{ "header": "Sexual Profile", "content": "Switch with a bratty side; ..." }],
  "exampleDialogue": [
    { "name1": "{{user}}", "content1": "Hi.", "name2": "Vesper Nyx", "content2": "Hey~" }
  ],
  "firstMessage": ["Need patching up? Sit down.", "Well, look who finally showed up."],
  "preInstruction": "roleplay",
  "preInstructionCustom": "",
  "reminderMessage": "Stay as Vesper Nyx; never speak as {{user}}.",
  "imagePrefix": "cell-shaded anime portrait, neon",
  "visualMap": "Vesper Nyx: pink hair with blue streaks, hazel eyes, curvy",
  "userOverride": { "name": "", "description": "", "avatar": null },
  "chatBackground": "https://.../bg.jpg",
  "bgBlur": "0",
  "bgOpacity": "0.2",
  "lorebook": { "lore_1710000000000": { "<LoreEntry>" } },
  "tags": ["tease", "roommate"],
  "lastModified": 1710000000000
}
```

Field rules:

- `id`: unique string, required on import. Shape is `char_<unique-id>` (e.g. `char_1710000000000`). The `char_` prefix is what matters — the example value is only an example, each character gets its own. It must be unique across the whole book. Any unique suffix works, even a short 9-digit one like `char_482913657`. Reusing an existing id overwrites that character, so always generate a fresh one.
- `name`: string, required. On save, `profile.name` is synced to `name`.
- `avatar`: URL string, or `""` for no avatar. `data:image/...` input gets hosted into a URL on save/export/share.
- `avatarSource`: optional full-res URL (max 1600px on upload). Removed when there is no crop source.
- `description`: short card blurb.
- `systemPrompt`: authoritative prompt. Can be empty. Structured mode overwrites it with the compiled output when profile/examples have content.
- `systemNoteDesc`: free-text scenario note.
- `profile`: all keys are strings (`name, age, gender, appearance, personality, background, scenario, systemNote`). Missing keys read as `""`. Dropped on save when fully empty.
- `customSections`: array of `{header, content}` at character level. Rows with empty header and content are dropped.
- `exampleDialogue`: array of 2-turn rows `{name1, content1, name2, content2}`. Rows with both contents empty are dropped. Dropped on save when fully empty.
- `firstMessage`: canonical form is an array of strings. Empty entries are dropped. A single string is accepted on input and normalized to a 1-element array. Import fallback is `["Hello."]`.
- `preInstruction`: string key. Known: `default`, `roleplay`, `roleplay_v2`, `urv_roleplay`, `author_mode`, `uncensored`, `the_operator`, `the_custodian`, `custom`. Unknown legacy values stay opaque (runtime falls back to the `roleplay` text). `custom` uses `preInstructionCustom`.
- `preInstructionCustom`: string. Only kept when `preInstruction === "custom"`; deleted otherwise.
- `reminderMessage`: post-history instruction.
- `imagePrefix`, `visualMap`: strings, can be `""`.
- `userOverride`: the user profile field (`{name, description, avatar}`). Prefer leaving it empty so the user can customize it in the app — only fill it when the user asks for a companion character. `avatar` is a URL or `null`; `data:image/...` gets hosted on save.
- `chatBackground`: URL string or `""`.
- `bgBlur`, `bgOpacity`: strings holding numbers (`"0"`, `"0.2"`).
- `tags`: string array.
- `lastModified`: ms epoch, rewritten on save.
- `favorite`, `folder`: local-only. Stripped on export, share, and hub publish.
- `{{char}}` and `{{user}}` work in `systemPrompt`, `firstMessage`, lore `content`, and scenario text.

## Structured compile

`profile` + `customSections` + `exampleDialogue` compile to:

```
# Character Profile: {{char}}

Name: ...
Age: ...
Gender: ...

## Appearance
...

## Personality
...

## Background
...

## <Custom Header>
...

## Scenario
...

[SYSTEM NOTE: single line]

[Example Dialogue]

<START>
{{user}}: ...
Vesper Nyx: ...
```

Only non-empty sections are emitted. `systemNote` newlines collapse to spaces. The `{{char}}` header stays literal. Each dialogue row renders as `<START>` followed by `Name: content` per non-empty turn; a nameless turn renders content alone. Profile and dialogue join with one blank line.

## LoreEntry

Map form: `char.lorebook` is an object keyed by entry id. Array form: `entries` in the native lorebook file. Same entry shape.

```json
{
  "id": "lore_1710000000000",
  "name": "The Red Pact",
  "keys": ["red pact", "blood oath"],
  "content": "The Red Pact binds ... {{char}} remembers ...",
  "priority": 80,
  "constant": false,
  "vectorized": false,
  "enabled": true,
  "excludeRecursion": true,
  "scanDepth": null,
  "keyEmbedding": null,
  "keyEmbeddings": null,
  "contentEmbedding": null
}
```

Field rules:

- `id`: unique string, must match its map key (shape `lore_<unique-id>`, e.g. `lore_1710000000000`). The example value is only an example. Keep it unique inside the lorebook — a clash silently overwrites the other entry.
- `name`: display name. Fallback `New Entry` / `Unnamed Entry`.
- `keys`: string array. Editor save splits on commas; blanks dropped. Can be empty (entry then only triggers via `constant` or vector match).
- `content`: lore text. Can hold `{{char}}` / `{{user}}`.
- `priority`: integer 1-100, default 10. Clamped on save/import.
- `constant`: always injected when `true` (if enabled).
- `enabled`: `false` leaves the entry out of retrieval.
- `excludeRecursion`: when `true`, the entry never triggers recursive discovery and never gets found through it. Prefer `true` when creating entries — it keeps one entry from accidentally pulling in other lores.
- `scanDepth`: `null` means use the global scan depth (default 4); otherwise integer 1-20, clamped.
- `vectorized`: when `true`, keys/content get embedded with multilingual-e5-small (384-dim) into `keyEmbeddings[]` + `contentEmbedding[]`. `keyEmbedding` is the legacy single-vector slot, cleared on re-save. When `false`, all three embedding slots are `null`. Native export drops embeddings; they get recomputed on import when `vectorized`.
- Retrieval: disabled entries are out; constant entries are in; keyword scan runs over the input + recent messages plus semantic match for vectorized entries; recursion expands matches unless excluded (max depth 2); final order is by priority; injection is capped at 2048 tokens with the `[Loaded: ...]` footer reserved up front; empty result is `""`.
