# URV Character JSON + Lorebook JSON

## Envelopes

JSON export file:
```json
{ "type": "urv-char", "version": 2, "data": { "<CharObject>" } }
```

Hub body payload:
```json
{ "type": "urv-char", "version": 3.0, "timestamp": 0, "data": { "<CharObject>" }, "meta": { "stripped_images": false, "hub_card": true } }
```

Chat export variant uses `type: "urv-char-chat"` and embeds `character` plus messages. Character-only tooling accepts the unwrapped `<CharObject>` directly.

`favorite` and `folder` are local-only. Stripped on export, share, and hub publish.

## CharObject

```json
{
  "id": "char_imported_1710000000000",
  "name": "Vesper Nyx",
  "avatar": "https://.../avatar.png",
  "avatarSource": "https://.../avatar-full.png",
  "description": "Short card blurb, max ~200 chars on import",
  "systemPrompt": "Authoritative behavior + identity. May be compiled from profile.",
  "systemNoteDesc": "Scenario / system-note-desc text",
  "profile": {
    "name": "Vesper Nyx",
    "age": "22",
    "gender": "Female",
    "appearance": "...",
    "personality": "...",
    "background": "...",
    "scenario": "...",
    "systemNote": "One-line system note"
  },
  "customSections": [{ "header": "Sexual Profile", "content": "..." }],
  "exampleDialogue": [
    { "name1": "User", "content1": "Hi.", "name2": "Vesper Nyx", "content2": "Hey~" }
  ],
  "firstMessage": ["First greeting.", "Alternate greeting 2"],
  "preInstruction": "roleplay",
  "preInstructionCustom": "",
  "reminderMessage": "Post-history instruction",
  "imagePrefix": "Style prefix + suffix joined by newline",
  "visualMap": "Appearance facts for image pipeline",
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

- `id`: string, unique. Presets use `char_preset_<Key>`; imports use `char_imported_<ms>`.
- `name`: string, required. Synced from `profile.name` in structured mode.
- `avatar`: URL string or `null`. `data:image/...` accepted on input, hosted to URL on save/export/share.
- `avatarSource`: optional full-resolution URL, max dimension 1600 on upload.
- `description`: short listing blurb. UIs truncate import sources to ~200 chars.
- `systemPrompt`: authoritative prompt. Empty allowed; structured compile overwrites it when profile/example content exists.
- `systemNoteDesc`: free text, joined with `profile.scenario` on Tavern PNG export.
- `profile`: all keys strings. Only `name` is load-bearing for sync; others optional. Missing keys read as `""`.
- `customSections`: array of `{header, content}`. Stored top-level on save (`char.customSections`), also read/written as `profile.customSections` in editor helpers. Rows with empty header and content are dropped.
- `exampleDialogue`: array of 2-turn rows `{name1, content1, name2, content2}`. Rows with both contents empty are dropped. Legacy single-turn `{name, content}` normalizes to turn 1.
- `firstMessage`: array of strings. Empty entries dropped. Single string imports normalize to 1-element array. Fallback `["Hello."]`.
- `preInstruction`: `default | roleplay | fast-paced | custom | <PRE_INSTRUCTIONS key>`. `custom` uses `preInstructionCustom`.
- `reminderMessage`: appended as post-history instruction.
- `imagePrefix`, `visualMap`: plain strings, may be `""`.
- `userOverride`: `{name, description, avatar}`. `avatar` URL or null; `data:image/...` hosted on save.
- `chatBackground`: URL string or `""`. Accepts `chat_background` alias on import path.
- `bgBlur`, `bgOpacity`: strings holding numbers (`"0"`, `"0.2"`).
- `tags`: string array.
- `lastModified`: ms epoch, rewritten on save.
- `hubSource`: card id string, present only after hub publish/import.
- Placeholders `{{char}}` and `{{user}}` valid in `systemPrompt`, `firstMessage`, lore `content`, scenario text.

Minimal valid character:
```json
{
  "id": "char_imported_1",
  "name": "Mira",
  "systemPrompt": "You are Mira, a blunt field medic.",
  "firstMessage": ["Need patching up? Sit down."],
  "userOverride": { "name": "", "description": "" },
  "lorebook": {},
  "tags": []
}
```

## Structured compile output

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
User: ...
Name: ...
```

Only non-empty sections emitted. `systemNote` newlines collapse to spaces. `{{char}}` header literal stays as-is.

`exampleDialogue` row renders as `<START>` followed by `Name: content` per non-empty turn. Nameless turn renders content alone.

## LoreEntry

Map form: `char.lorebook` is object keyed by entry id. Each value:

```json
{
  "id": "lore_1710000000000",
  "name": "The Red Pact",
  "keys": ["red pact", "blood oath"],
  "content": "The Red Pact binds ... {{char}} remembers ...",
  "priority": 10,
  "constant": false,
  "vectorized": false,
  "enabled": true,
  "excludeRecursion": false,
  "scanDepth": null,
  "keyEmbedding": null,
  "keyEmbeddings": null,
  "contentEmbedding": null
}
```

Field rules:

- `id`: string key, must match its map key (`lore_<ms>[_<rand>[_<idx>]]` by convention).
- `name`: display name. Fallback `New Entry` / `Unnamed Entry` / `Lore Entry N`.
- `keys`: string array. Comma-split on editor save; blanks dropped. Empty array allowed (entry then triggers only via `constant` or vector match).
- `content`: lore text. May contain `{{char}}` / `{{user}}`.
- `priority`: integer 1-100, default 10. Clamped on save/import.
- `constant`: always inject when true (if enabled).
- `enabled`: `false` excludes entry from retrieval.
- `excludeRecursion`: when true, entry never triggers recursive discovery and never gets discovered recursively.
- `scanDepth`: `null` = use global scan depth; else integer 1-20, clamped.
- `vectorized`: when true, keys/content embedded with multilingual-e5-small (384-dim). Stores `keyEmbeddings[]` + `contentEmbedding[]`. `keyEmbedding` is legacy single-vector slot, cleared on re-save.
- Import aliases accepted: `key`, `secondary_keys`, `keysecondary` merged into `keys` (case-insensitive dedupe); `insertion_order` / `order` / `depth` map to `priority` / `scanDepth`; `disable: true` maps to `enabled: false`; `exclude_recursion` maps to `excludeRecursion`; `comment` maps to `name`; ST depth `4` means null.
- Retrieval: disabled excluded; constant included; keyword scan over scan buffer plus semantic match for vectorized entries; recursion expands matches unless excluded; final ordering by priority; injection budgeted at 2048 tokens with `[Loaded: ...]` footer reserved up front; empty result yields `""`.

Minimal valid entry:
```json
{
  "id": "lore_1",
  "name": "Town",
  "keys": ["Millhaven"],
  "content": "Millhaven is a flooded mill town.",
  "priority": 10,
  "constant": false,
  "vectorized": false,
  "enabled": true,
  "excludeRecursion": false,
  "scanDepth": null,
  "keyEmbedding": null
}
```

## Import mappings

SillyTavern `chara_card_v2/v3` (`name, description, personality, scenario, first_mes, mes_example, alternate_greetings, system_prompt, post_history_instructions, tags, character_book.entries[]`) converts to URV with `description=personality(+creator_notes)`, `systemPrompt=description(+mes_example)`, `systemNoteDesc=scenario`, `profile.appearance=description`, greetings array, lorebook normalized via alias rules above.

Perchance Dexie `{formatName:"dexie", data.data[]}` table `characters` row 0 maps `roleInstruction` to `systemPrompt`, AI `initialMessages` to `firstMessage`, role-instruction user entry to `userOverride`, `imagePromptTriggers` to `visualMap`, prefix+suffix to `imagePrefix`.

FurAI `{chat, profile:{user, bots[]}}` imports first bot only. `instruction` becomes `systemPrompt`; `Appearance:/Personality:/Background:` sections parsed into profile; opening AI message becomes `firstMessage`; `openingBackground/background` becomes `chatBackground`.

Embedded lorebook sources accepted: `character_book.entries`, `world.entries`, `node.definition.embedded_lorebook.entries`, legacy `node.entries` / `node.content` JSON.

## Tavern PNG export mapping

URV to Tavern card:

- `name` to `name`
- `systemPrompt` to `description`
- `description` to `personality`
- `systemNoteDesc` + `profile.scenario` joined to `scenario`
- `firstMessage[0]` to `first_mes`
- full `firstMessage` array to `alternate_greetings`
- `reminderMessage` to `post_history_instructions`
- `preInstructionCustom` (only when `preInstruction==="custom"`) to `system_prompt`
- `tags` to `tags`
- `mes_example` empty; `creator: "URV User"`; `character_version: "2.0"`
