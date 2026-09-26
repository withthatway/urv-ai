# URV AI — Documentation

## 1. Platform Overview

URV (Understanding · Reasoning · Value) is a multimodal AI environment: roleplay/storytelling, text-to-image / image-edit / video, TTS voice, per-thread agent file workspace, community hub, themes + 11 languages.

Model architecture:

- Free tier (default, no key): **DeepSeek-R1-0528** — single multimodal engine for text + reasoning + vision. Image: **Flux-Schnell**.
- Paid / connected providers (set in Menu → Your Profile): **Pollinations AI**, **DeepSeek AI**, **TomdacatAI**, or any OpenAI-compatible **custom endpoint**. Unlocks larger model library, image editing, video.

Safety policy:

- **URV adds no content filter of its own.** Free-tier engines (DeepSeek-R1-0528, Flux-Schnell) run unfiltered — may produce NSFW/explicit/sensitive content if prompted.
- Third-party models via connected provider may apply their own upstream policies.
- User assumes full responsibility for outputs.

Capabilities map:

- Roleplay & Storytelling: Character Book, lorebooks, memory, branching history, group chat.
- Image & Video: text-to-image, image edit, image-to-video, auto-illustrated replies.
- Voice: 5 TTS engines, per-model voices, read modes.
- Agent Workspace: per-thread files the model can read/write/search (Editor Mode).
- Community: publish/browse characters, forum, shared galleries.
- Personalization: themes, 11 languages, personas, UI scaling.

## 2. Model Registry

One dropdown lists every engine reachable through the selected provider. Filter with capability tabs, search box, sort button.

Providers:

- **Free** — DeepSeek-R1-0528, stable default. Perchance + WithThatWay infra. No key/account.
- **Pollinations AI** — largest library: Claude, Gemini, GPT, DeepSeek, GLM, Kimi, Llama, plus image-edit and video models.
- **TomdacatAI** — third-party gateway, own catalogue.
- **DeepSeek AI** — direct DeepSeek endpoint.
- **Custom endpoints** — any OpenAI-compatible URL, self-built model list.

Capability tags:

| Tag | Meaning |
|-----|---------|
| Text | Text generation only |
| Hybrid | Multimodal — understands uploaded images + text (vision) |
| Reasoning | Thinking / chain-of-thought pass, Extended Thinking toggle + Reasoning Effort selector |

Finding a model:

- Search matches model id, display name, provider key, provider label.
- Sort cycles A→Z / Z→A / Default. Choice remembered.
- Pinned (popular) models sit on top with flame icon regardless of sort.
- Trigger shows Think badge (reasoning) and Vision badge (hybrid).
- Custom endpoint: attach provider logo, mark each model Reasoning and/or Vision so correct controls appear.

## 3. Command Reference

Type `/` at start of chat input to open command palette. Commands accept optional args after a space.

| Command | Effect |
|---------|--------|
| `/nar [prompt]` | Narrator Mode. Narrator voice advances plot / describes scene outside any character POV. |
| `/ai [instruction]` | Override Generation. Forces active AI/character to respond; arg steers tone/content that turn. |
| `/user [action]` | Impersonation Mode. Writes action/line as you (active persona), then triggers AI reply. |
| `/image [prompt]` | Generative Request. Imagine mode, renders visual asset inline. |
| `/char [instruction]` | Character Reply. Forces active character to respond; directs speaker in multi-char threads. |
| `/system [directive]` | System Injection. Silent instruction into context window, no visible message. |
| `/memory` | Memory Anchors. Opens pinned-context manager. |
| `/sum` | Summary Editor. Hierarchical summary manager, view/edit/trigger compression. |
| `/visualmap` | Visual Map. Appearance registry Auto Image Gen reads. |
| `/voice` (alias `/tts`) | Voice Configuration. TTS panel: model, voice, read mode. |

Input methods:

- Text / Code: main chat bar.
- `@` Mentions: type `@` to summon another character in group thread.
- Vision: `+` button attaches 1+ images for multimodal analysis on hybrid/vision model.
- Imagine: input becomes image prompt routed to image panel (text-to-image / edit / video).
- Web Search: RAG retrieval before generation (SearXNG).
- Editor Mode: file workspace tools.
- Document: attach PDFs/documents for analysis/summarization.

Thinking & Effort:

- Reasoning-capable models show Thinking toggle in composer.
- When on, Effort selector appears (Standard up to Extended where allowed).
- Thinking off = faster, shorter replies; on Free-tier model falls back to single-pass generation.

## 4. Multimodal APIs

### Computer Vision

- Natively handled by hybrid/vision models. Attach images with `+`, they travel with that turn.
- Any Hybrid-tagged model (or Vision-flagged custom model) understands them directly — no separate vision model.
- Free default DeepSeek-R1-0528 is itself multimodal.
- If a text-only model is selected, vision turns are handled by a vision-capable model instead.

### Live Retrieval

- Engine: **SearXNG**.
- Bypasses static knowledge cutoff. When Search Mode enabled, model queries SearXNG, retrieves/synthesizes/cites real-time web info before answering.

## 5. Visual Generation

Image panel (`+ → Imagine` or `/image`) is a 3-mode studio, switched via tabs:

- **Text → Image**: natural-language prompt → fresh image. Free tier: Flux-1 Schnell (NSFW). Pollinations adds Ideogram, DreamShaper, Z-Image Turbo, Krea, Pruna-P, DEV Flux build.
- **Edit (Image → Image)**: up to 4 reference images + change description. Engines: NanoBanana (Gemini Image), xAI Grok Imagine, Flux Kontext / Flux 2, GPT-Image, Qwen-Image, Seedream, Amazon Nova Canvas.
- **Video**: Image → Video from 1 reference frame, ~5–120s clip depending on engine: Wan, Google Veo 3.1 Fast, Seedance, MiniMax-H3, Grok Video, Amazon Nova Reel, Pruna P-Video.

Prompting:

- Use natural language descriptions, not tag soup.
- Good: "A cinematic wide shot of a cyberpunk city at night with neon rain, highly detailed."
- Avoid: "cyberpunk, city, neon, rain, 8k, best quality, masterpiece"
- Aspect ratio: grouped catalogue (~30 presets: Quick, Photography & Social, Cinematic & Ultrawide, Rare & Specific) covering 1:1, 16:9, 9:16, 21:9 up to 9:32 extremes, each with concrete resolution.

Other:

- Character Injection: active character visual definitions + Visual Map auto-appended when that character is the subject.
- Saving: generated images ephemeral by default. Click Keep → stored in My Gallery (local IndexedDB), survives reloads, never uploaded.
- Auto Image Gen: image on every reply without prompting (see §7).

## 6. Voice & Speech

TTS served by **Pollinations audio API**. Browser fetches 1 audio clip per sentence, plays gaplessly. Nothing to install.

- Per-message waveform button reads that message aloud.
- Full panel: `/voice` or `/tts` — model, voice, read mode. Choices remembered per model, survive reload.

Read Mode:

| Mode | Behavior |
|------|----------|
| Dialog (default) | Reads only quoted dialogue. Best for roleplay; skips narration/stage directions. |
| Full | Reads entire message, Markdown stripped. |

Style Instructions: some engines accept short style instruction (tone/pace/emotion, e.g. "soft, warm, unhurried"), stored per model. Unsupported engines hide the field.

Voice engines:

| Engine | Voices / notes |
|--------|----------------|
| Kokoro-82M (default) | 54 voices, 8 langs (EN, ES, FR, HI, IT, JA, PT, ZH). Compact expressive open model, grouped by native language. |
| Grok TTS | 28 voices, supports style instructions. Widest roster. |
| Qwen3-TTS-Flash | 20 voices. Fast clean narration. No style instructions. |
| Qwen3-TTS-Instruct-Flash | 12 voices, supports style instructions. |
| Fish Audio S2.1 Pro | No voice picker — instruction-only delivery. |

Test without account: live synthesis needs connected Pollinations account. If not connected, Test button plays pre-rendered sample clip per voice.

Caching: every clip cached on-device by hash of exact text — replaying same message/sentence is instant and free. Panel shows storage used + Clear cache.

## 7. Auto Image Gen

Storyboard mode: every assistant reply illustrated. Model first describes scene, image rendered from description, prose woven around it.

- Enable: Menu → Image Gen. Second row chooses which image engine illustrates scenes. Per-chat setting.
- Visual Map: built from character sheet before first render — compact look record per character, reused whole thread. Open with `/visualmap` to view/correct names, relationships, identities.
- NPC Registry & Aliases: mid-story characters added with relationship keywords/aliases (e.g. "mom", "mother" → Sarah). Mentioning alias in chat/frame directive/other entry resolves to same frozen identity. Cast kept in sync between writing and render.

## 8. Access Tiers & Custom Endpoints

Fully usable with no account. Connecting provider only widens availability.

Free Tier (default):

- No keys/accounts. Perchance + WithThatWay infra. Trade-off: shorter per-session memory, slower generation.
- Includes: DeepSeek-R1-0528 (text/reasoning/vision), Flux-1 Schnell [NSFW] image gen.

Connect a Provider (all in Menu → Your Profile):

| Provider | Notes |
|----------|-------|
| Pollinations AI | Largest library. Sign in, top up privately, pay per use. Bigger context, image edit, video. |
| TomdacatAI | Third-party gateway, own model list. |
| DeepSeek AI | Direct DeepSeek endpoint — paste API key. |
| Custom Endpoint | Any OpenAI-compatible URL (SDK base URL or full chat/completions URL), choosable auth header, provider logo, model manager. |

Custom model entries: flag each Reasoning and/or Vision + Reasoning parameter style. URV shapes request per vendor and strips rejected params (e.g. "thinking.type" vs "reasoning_effort" both work).

Keys stay on-device: provider keys stored locally in browser, sent only to configured endpoint — never to URV/Perchance.

## 9. Context Management

User Identity Scope:

| Scope | Behavior |
|-------|----------|
| Global User | Persistent profile in Your Profile. Default for all new sessions. |
| Persona User | Local override in Character Book. Supersedes Global User only while that character context active. |

Character Import & Export — 2 portable formats:

- **JSON Character Card** (native URV): all data — system prompt, persona, lorebook, visual defs, settings — in one `.json`. Use: Character Book → select character → Export / Import.
- **SillyTavern PNG V2 Card**: PNG with embedded `chara` metadata (Base64 JSON in tEXt chunk) imported directly, data auto-extracted. Use: Character Book → Import → select `.png`. Embedded lorebook imported too.
- Cross-platform: SillyTavern, Chub, any Tavern V2 spec imports without manual conversion. Lorebook entries, example dialogues, persona preserved.

## 10. Lorebook Management

Dynamic knowledge injection: entries auto-injected into context window when trigger keywords appear — no permanent context cost.

- Keyword Triggering: comma-separated trigger list per entry. Activates when any keyword appears in last N messages (Scan Depth, default 4), injected next context build. Ex: `Excalibur, magic sword, the blade`.
- Semantic Match: optional per-entry vector-similarity trigger on meaning, not exact words (e.g. "sword" + "blade" → same weapon entry). Must re-save entry after enabling to generate embedding.

Entry settings:

| Field | Meaning |
|-------|---------|
| Entry Title | Sidebar label only. AI never sees it. |
| Lore Content | Injected text. Factual/concise — AI treats as authoritative world info. |
| Priority | 1–100. Higher loads first under limited budget. 80–100 = critical world rules. |
| Scan Depth | Recent user messages scanned. Blank = global default (4). |
| Always Active | Injected every turn regardless of keywords. Core rules/traits. |
| No Chain Trigger | Triggerable by chat only, not by keywords inside other lore content. Prevents recursion loops. |
| Enabled toggle | Disable without deleting. |

Import & Export:

- Export: Lorebook → Export → `.json` all entries for active character. SillyTavern-compatible.
- Import: `.json` from any Tavern-compatible source merged in, existing entries not overwritten.
- PNG V2 Embedded Lore: importing V2 PNG with embedded lorebook auto-imports all lore entries.

Context budget: all triggered lore shares a char budget (default 2500). Over budget → lowest-priority dropped. Use Context Snapshot tool to inspect injected entries, usage, cuts last turn.

## 11. Version Control

- Branching Tree: non-linear history of all interactions. Every edit/regeneration = new branch node. Navigate via Conversation Map to restore previous context states.
- Diff Analysis: side-by-side compare of divergent generations. Pick optimal output by semantic accuracy or creative variance.

## 12. Developer Utilities

- Context Snapshot: full constructed context sent to inference engine — tiers, lore injections, memories, summaries. Debug OOC drift / token overflow.
- System Sticky: persistent instruction block at end of prompt chain, never lost to window sliding.
- Sandbox: isolated HTML/JS rendering. LLM-generated code executes safely in Artifact Preview window.

## 13. Editor Mode & Workspace

Chat becomes working agent with own file workspace. Model creates/reads/searches/edits files — builds/revises docs, code, structured data across turns.

- Requires external model: unavailable on Free-tier model; needs tool/function-calling-capable model.
- Turn on: (1) select external model, (2) open `+ → Editor Mode`, (3) paperclip attaches up to 10 documents into workspace. Workspace is per-chat.
- Workspace Panel: Menu → Your Workspace. Browse files/collapsible folders, new file/folder from scratch, upload from device, built-in code editor, send workspace file into chat as attachment.
- Agent behavior: opens existing files, reads, project-wide definition search, writes/splices proposed changes, re-reads own work to confirm. Can draft scripts/configs/datasets/documents, rename/copy/reorganize into folders, fetch file from URL, view stored image, hand shareable link to output. Every action shown as expandable card in conversation.

## 14. Community Hub

Header Community Hub button opens 5-tab modal: **URV Hub, Forum, Public Gallery, My Gallery, Resources**. Top strip: live presence (online counter, clickable per-region breakdown, GitHub + Discord links). Auto-refreshes grid/counter/outbox.

- URV Hub: shared character index. Search, sort (recent/updated/top-rated/most-downloaded/name), tag-click filter, NSFW toggle (off default). Detail view: description, author, comments, live download counter. Import to Book / Import & Chat. Rate 1–5 stars, report, fork chips back to original.
- External Browse: source toggle → External browses remote DBs (ChubAI) in-app — characters + lorebooks, multi-topic filter, sorts, own NSFW toggle. Import straight to Character Book (Tavern V2/V3 PNG or URV export). One-click send to colleague platform e.g. FurAI.
- Publishing & Identity: create author identity first — profile + passphrase derive Profile ID locally, ownership follows across devices, server never sees passphrase. Publish/update/remove characters, offline outbox auto-flushes on reconnect. Restore identity / moderator-issued Recover access relinks characters if profile lost.
- Forum: multi-channel board: #General, #Roleplay, #18+, #Report Bug, #Spam, What's New? Each its own comment stream with file attachments; italic/bold/inline-code converted to Unicode to survive plain-text feed.
- Galleries & Resources: Public Gallery = community-shared images (multi-select + moderation); My Gallery = your kept images, local IndexedDB; Resources = embedded project resource page.

## 15. Personalization

All in header menu, auto-saved to browser.

- Theme Customizer: presets per mode + full color picker for dark/light separately. Design: font family/size, border radius. Chat: assistant bubble style (none/surface/tint/outline), avatar shape (square/circle/portrait/hidden), custom user & AI bubble colors + opacity slider. Character Background shortcut for active chat.
- Language & Scale: 11 languages — English, Bahasa Indonesia, Deutsch, Français, 中文简体, Русский, 日本語, हिन्दी, Español, العربية, فارسی — RTL for Arabic/Persian. UI Scale slider 55%–140%. Light/dark + fullscreen toggles beside it.
- User Profiles (Personas): multiple personas — tab label, avatar, chat name, system-prompt context block each. Active persona = what AI understands "you" to be. Character's User Profile tab can Import from Global Profiles in one click. Any thread can override persona (name/avatar/system prompt) without touching global profile.

## 16. Session & Memory Tools

Per-conversation steering without editing the character.

| Tool | Notes |
|------|-------|
| Memory Anchors (`/memory`) | Pin critical chunks so never dropped as window slides. |
| Summaries & Auto-Memory (`/sum`) | Compress long history into hierarchical summaries, self-editable. Auto-Memory Learning auto-extracts new facts; disable if AI dwells on trivia. |
| Sticky Reminder | Hidden per-thread rule (e.g. "no emojis") always appended near prompt end, preset library. |
| Thread Configuration | Local snapshot per chat: override AI name/avatar/system prompt, user name/avatar, reminder, Narrative Density length control (1–5). |
| Conversation Map | Branching tree of every edit/regeneration. Jump to earlier state, diff divergent replies side-by-side. |
| Search & Archive | Global search overlay across all threads; Archive hides chats from sidebar without deleting. |
| Code Viewer & Artifacts | Code blocks in highlighted viewer with Copy + Edit in Workspace; HTML/JS artifacts in sandboxed preview. |
| Group Chat | Multiple characters in one thread with cast bar; `@` to hand turn. |
| Data Import / Export | Export/import all data from sidebar actions, or factory reset. All local to browser. |

## 17. Terms of Service

Last Updated: 2026-09-21. Platform: `perchance.org/urv-ai-chat`. Developer: WithThatWay Project. Infra: Perchance.org.

1. Acceptance: accessing URV AI Chat = read/understood/agree.
2. Service: text gen via AI models, image/video/voice gen, roleplay/storytelling, per-thread agent workspace, shared character hub + publishing, user-managed chat.
3. Age: ≥18, or ≥13 with explicit parental/guardian supervision + consent.
4. Content Policies. NSFW warning: models may generate NSFW when prompted; some have filters, some don't; user solely responsible for viewed content + legal compliance. Prohibited: violating jurisdiction laws; IP infringement; sexually exploitative content involving minors; real-person likeness without consent; harassment/bullying/threats; fraud/malicious use.
5. Data Privacy. Local Storage: chats/settings/history in device IndexedDB — clearing browser data deletes permanently. External APIs: prompts via external APIs may process on third-party servers — never input PII/financial/passwords. Public = published: Hub character index entry + hosted body file, author profile, forum posts, gallery images are public on public servers. Everything else (threads, workspace, galleries, provider keys, personas) stays local. Never publish personal/sensitive info.
6–11. General: Terms updatable anytime, continued use = acceptance. Access suspendable on violation. AI content unverified — user assumes reliance risk. Contact: `perchance.org/withthatway`.
