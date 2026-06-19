# Suno Knowledge Base
**u/olddertybasterd's Centralized Suno Prompting Reference**
*Last updated: June 19, 2026 (Sonic 1 DNA string corrected)*

---

## About This Document & Our Workflow

This reference was built over the course of multiple Suno AI music production projects and is shared here in the hope that it helps others working in a similar space. All community findings are attributed to their original authors. Live testing notes reflect our own empirical results.

### Our Workflow — Per Project (OST Level)
We are producing **full synthwave soundtrack overhauls** of classic video game OSTs. Each project covers an entire game soundtrack — anywhere from 26 to 112 tracks — and is treated as a cohesive album with a unified sonic identity. Before any generation begins, we establish:
- A **Universal DNA String** — a locked set of core descriptors shared across every track in the project
- A **reference map** — identifying audio references per track suitable for use as Suno reference uploads
- A **production framework document** — covering sonic identity, genre cluster, key prompt learnings, and per-track generation notes

Projects completed or in progress: Sonic 3 & Knuckles (mixing), A Link to the Past (complete), Super Mario World (mixing), Majora's Mask (in progress), Yoshi's Island (generation complete, 26/26 tracks locked — moving to mixing), Ocarina of Time (in progress), Sonic 1 (DNA locked, pre-generation), Sonic 2 (pending).

### Our Workflow — Per Track
For each individual track in a project:
1. **Reference sourcing** — find a suitable cover arrangement to use as a Suno reference upload (see Section 7 for reference type guidance)
2. **Prompt building** — construct the style field using the Universal DNA String + track-specific descriptors + categorized anti-prompts
3. **Lyrics field** — populate with `[Disc_]` channel headers (macro timbral direction) + bare arrangement markers
4. **Generation** — minimum 3 outputs per run in Suno v5.5 Advanced/Custom mode, Instrumental checked
5. **Stem selection** — identify the best output(s) from each batch as leaders
6. **Mixing** — selected stems are imported into Ableton Live 12 Standard for final mixing. No compression on the master bus — EQ Eight + Saturator, master Limiter at –1.0 dB True Peak

### Tools
- **Suno AI v5.5** — generation
- **Ableton Live 12 Standard** — mixing
- **`analyze_stem.py`** — custom Python script using ffmpeg/numpy/scipy to detect BPM and key of reference audio and generated stems
- **Windows 11 / RTX 2070** — production environment

---

## Table of Contents
1. [Core Prompt Architecture](#1-core-prompt-architecture)
2. [Style Field Rules](#2-style-field-rules)
3. [Lyrics Field Rules](#3-lyrics-field-rules)
4. [Anti-Prompt Categories](#4-anti-prompt-categories)
5. [Genre Tag Rules](#5-genre-tag-rules)
6. [Universal DNA Strings (Per Project)](#6-universal-dna-strings-per-project)
7. [Generation Tips](#7-generation-tips)
8. [Cross-Project Prompt Learnings](#8-cross-project-prompt-learnings)
9. [Reddit Findings Log](#9-reddit-findings-log)

---

## 1. Core Prompt Architecture

Two fields matter: **Style** and **Lyrics**. They do not communicate with each other.

| Field | Purpose | Hard Limit |
|---|---|---|
| Style | DNA string + fine detail + anti-prompts | ~1000 characters |
| Lyrics | `[Disc_]` channel headers + arrangement markers | No natural language text |

**Current baseline (Optimized Hybrid):** `[Disc_]` channel headers in the lyrics field handle macro timbral direction. The style field handles fine detail, identity, and all negatives. Both together produce richer, more layered output than either alone — confirmed by live testing June 2026. *(Live testing: June 2026 — Finding Set 7)*

**Division of labor:**
- **`[Disc_]` channels = macro** — broad tonal direction, spatial positioning, energy register, overall sonic world *(Live testing: June 2026 — Finding Set 7)*
- **Style field = fine tuning** — specific textures, exact BPM/key, named rhythmic details, hook motifs, anti-prompts *(Live testing: June 2026)*

**Note on pipe symbol `|` in style field:** Tested June 2026 — no quality improvement, amplifies guitar reference bleed. Comma-separated values confirmed as correct standard. *(Live testing: June 2026 — Finding Set 10)*

---

## 2. Style Field Rules

### Format
Comma-separated positive descriptors as a continuous block, then each `no_` category on its own line beneath. Target **820–900 characters**, hard ceiling **1000**.

### Canonical Format (confirmed June 2026)
```
[genre stack], [project DNA], [zone-specific descriptors], [texture/rhythm/mix detail]
no_structure: [exclusions]
no_genre: [exclusions]
no_mix: [exclusions]
no_master: [exclusions]
no_vocal: [exclusions]
```

The line break separation between positive descriptors and each `no_` category is meaningful — each category on its own line is processed as a discrete instruction. Do not run them together in one line. *(Live testing: June 2026 — observed across YI production session)*

**Critical rule — negative category token count:** Keep each `no_` category to a single broad token where possible. Categories with ~5 or more specific tokens carry a confirmed risk of backfiring — pulling the excluded concept *into* the output rather than excluding it. This held across multiple unrelated genres and tracks. *(Live testing: June 2026 — Finding Set 11, fully resolved)*

**Alternative style field structure (not combined with `[Disc_]` channels):** An arrow-delimited "signal chain" structure — `{rhythm} → {bass} → {harmony} → {melody} → {mix/production}` — produces a distinct, independently valuable result when used **in place of** the standard hybrid (i.e., with bare lyrics markers, no `[Disc_]` channels). Do not combine this with `[Disc_]` channels — confirmed to produce inconsistent batch results when stacked. *(Live testing: June 2026 — Finding Set 13; technique sourced from Finding Set 12 external research)*

### Structure Order
1. **Genre stack** (dominant genre first — see Section 5) *(Reddit: u/sunoarchitect — Finding Set 2; Reddit: r/SunoAI v5.5 study — Finding Set 1)*
2. **Project DNA string** (faithful cover declaration + core sonic identity) *(Production: S3K, ALttP, SMW, MM, OoT project logs)*
3. **Album version append:** `wide stereo field, atmospheric depth, dynamic range, cinematic production` *(Production: S3K project log)*
4. **Zone/track-specific signal:** key, BPM, lead character, texture descriptors, rhythm descriptors *(Established practice)*
5. **Categorized anti-prompts on separate lines** (see Section 4) *(Reddit: r/SunoAI v5.5 study — Finding Set 1)*

### Key Style Field Rules
- **"Faithful cover" must be in every prompt** — meaningfully improves Suno's melodic accuracy. Non-negotiable across all projects. *(Production: S3K project log)*
- **No artist names** — Suno blocks them in the style field. Translate artist fingerprints into descriptive sonic language instead. *(Production: S3K project log)*
- **Most dominant genre goes first** — Suno reads sequentially. Lead with the dominant genre; it shapes the center of gravity. *(Reddit: u/sunoarchitect — Finding Set 2)*
- **Genre tag order matters** — 2–3 complementary genre tags outperform a single tag, but order shapes output. `[Synthwave, Dark ambient, Cinematic]` ≠ `[Cinematic, Synthwave, Dark ambient]`. *(Reddit: u/sunoarchitect — Finding Set 2; Reddit: r/SunoAI v5.5 study — Finding Set 1)*
- **Reference audio key overrides your prompt key** — stems land in the reference's key regardless of what you specify. Always analyze the reference's actual key with `analyze_stem.py` first. *(Production: S3K project log)*
- **Describe textures, not just instruments** — "dusty piano, warm bass, glassy synths, tape hiss" outperforms "piano, bass, synths." Suno responds strongly to texture words. *(Reddit: u/SevenSky_Tech1 — Finding Set 5; Production: S3K, ALttP project logs)*
- **Name instruments specifically** — genre labels evaporate when Suno can't resolve them to a specific sonic identity. Named instruments (log drum, rimshot, woodblock, supersaw) survive where genre names don't. *(Reddit: u/BryanP7737 — Finding Set 4)*
- **Describe rhythm mechanically, not by genre label** — if the feel comes from a specific pattern, spell it out. Suno defaults to filling every beat. *(Reddit: u/BryanP7737 — Finding Set 4)*
- **Stick to 4–7 descriptive words per concept** — too much text confuses Suno. *(Reddit: u/SevenSky_Tech1 — Finding Set 5)*
- **Suno struggles to hold two conflicting emotions simultaneously** — achieve emotional balance in the DAW, not the prompt. *(Production: ALttP project log)*
- **For kinetic/immediate-entry tracks**, replace `cinematic production` with: `immediate energy from first second, hits hard from bar one, triumphant entry, no build-up, no intro swell, no slow start` *(Production: ALttP project log)*
- **Guardrails (apply when bleeding into acoustic territory):** `100% synthesized, analog synth, no real instruments, no live orchestra, no acoustic instruments` *(Production: ALttP, MM project logs)*

### What We Tested and Rejected
The full Reddit key:value structure (`genre: [...]`, `energy: [...]`, etc.) was A/B tested on Green Hill Zone vs. flat tag list — **no measurable quality difference**. Flat format retained for character budget. The `no_` category labels were the one keeper. *(Reddit: r/SunoAI v5.5 study — Finding Set 1; Live testing: June 2026)*

---

## 3. Lyrics Field Rules

### Current Baseline Template (Optimized Hybrid)
```
[Disc_Lead: Macro_Lead_Character | Energy_Register | Stereo_Width_Maximum]
[Disc_Bass: Macro_Bass_Character | Groove_Direction | Mono_Sub_Lock]
[Disc_Rhythm: Macro_Rhythm_Character | Dynamic_Feel | Center_Mono]
[Disc_Pad: Macro_Pad_Character | Atmosphere | Stereo_Width_Maximum]
[Disc_Mix: Stereo_Width | Dynamic_Range | spatial_notes]

[Intro]
[Verse]
[Chorus]
[Verse]
[Chorus]
[Breakdown]
[Verse]
[Chorus]
[Outro]
[End]
```

**`[Disc_]` channel rules:**
- Channels handle **macro timbral direction only** — broad strokes, spatial positioning, energy register *(Live testing: June 2026 — Finding Set 7)*
- Fine detail (specific textures, BPM, key, hook motifs) stays in the style field where it has more impact *(Live testing: June 2026)*
- Use underscore syntax — `Sparkle_Arpeggio` not "sparkle arpeggio" — confirmed non-vocalizing *(Live testing: June 2026 — Finding Set 7)*
- Pipe `|` delimiter separates each descriptor as a discrete instruction *(Reddit: u/Zestyclose_Basil_349 — Finding Set 7; Reddit: u/Grenar — Finding Set 10)*
- Channel order matters — channels declared earlier get more generative weight. Put most important first *(Reddit: u/Zestyclose_Basil_349 — Finding Set 7)*
- Max ~5 channels; keep each to 3–5 descriptors for clarity *(Live testing: June 2026)*
- `[Disc_No]` channel for negatives does **not** work reliably — keep all negatives in the style field *(Live testing: June 2026 — Finding Set 7)*

### Standard Arrangement Markers
```
[Intro]
[Verse]
[Chorus]
[Verse]
[Chorus]
[Breakdown]
[Verse]
[Chorus]
[Outro]
[End]
```

### Rules
- **`[Disc_]` channel headers do not vocalize** — confirmed across multiple test passes June 2026. Underscore syntax is parsed as metadata. *(Live testing: June 2026 — Finding Set 7)*
- **Natural language text in the lyrics field vocalizes** — confirmed. No prose, no descriptive sentences, no natural language of any kind. *(Production: S3K, ALttP project logs)*
- **Natural language pipe-delimited section headers do not vocalize but are not directive** — tested June 2026. Not adopted. *(Live testing: June 2026 — Finding Set 10)*
- **`[End]` is mandatory on the final line** — without it, Suno generates past the natural finish or cuts awkwardly at 4 minutes. *(Reddit: u/sunoarchitect — Finding Set 2)*
- **Two verse/chorus cycles before the breakdown** — gives the track room to establish melodic identity. *(Production: S3K, ALttP project logs)*
- **EDM drops and breakdowns are welcome** in album versions — `[Breakdown]` pulls toward dynamic energy drop. *(Production: S3K project log)*
- **Meta style tags belong BEFORE the section header, not after** — Suno reads top-to-bottom and commits to a section on hitting the header. *(Reddit: u/sunoarchitect — Finding Set 2)*

---

## 4. Anti-Prompt Categories

Categorized negatives outperform flat NO lists. The category label acts as a semantic anchor — it tells Suno *why* not, not just *what* not. From the original Reddit study: this was the single most impactful finding, driving scored output from 4/11 to 10/11. *(Reddit: r/SunoAI v5.5 study — Finding Set 1)*

### Standard Format
```
no_structure: [festival build-and-release, generic EDM drop texture, abrupt cuts]
no_genre: [darksynth, minor key drift, trap]
no_mix: [sub-dominated mix, muddy mids]
no_master: [loudness war, squashed dynamics]
no_vocal: [lyrics, autotune, sung melody]
```

### Intro Hum Blocklist
Suno frequently opens tracks with humming/wordless vocalizing — a known AI music "tell." Block via the Exclude Styles field or `no_vocal` category:
```
humming, hums, mmm vocals, ooh vocals, ahh vocals, la la vocals, wordless vocals,
vocalizing before lyrics, intro vocals, spoken intro, whispered intro, ad-lib intro,
instrumental intro, ambient intro, cinematic intro, long intro
```
*(Reddit: u/PureRely, r/SunoAI — 58 upvotes — Finding Set 5)*

### Rules
- Adjust `no_genre` per track/project. `darksynth` is excluded for bright projects but is the *standing co-genre* for dark clusters (Ikana/endgame in MM, Dark World in ALttP, Valley of Bowser in SMW). *(Production: ALttP, SMW, MM project logs)*
- Always include even when the style field is tight — these add semantic value at minimal character cost. *(Reddit: r/SunoAI v5.5 study — Finding Set 1)*
- Negative prompts are essential steering tools. Always include `no vocals` and mood-appropriate negatives per track. *(Established practice)*
- **Describe what's absent, not just what's present** — if a genre's identity comes from a missing element, explicitly prompt for the absence. Suno fills silence by default. *(Reddit: u/BryanP7737 — Finding Set 4)*

---

## 5. Genre Tag Rules

- **2–3 complementary genre tags outperform a single tag** — stacking creates a blended tonal identity. *(Reddit: r/SunoAI v5.5 study — Finding Set 1)*
- **Order determines center of gravity** — first tag is weighted most heavily. Lead with dominant genre. *(Reddit: u/sunoarchitect — Finding Set 2)*
- **Future bass** — standing co-genre for bright/propulsive tracks. Adds low-mid bounce and emotional chord swells without losing melodic identity. *(Production: S3K, ALttP project logs)*
- **Darksynth** — standing co-genre for menacing/endgame tracks. Adds weight without sacrificing melodic fidelity. *(Production: ALttP, SMW, MM project logs)*
- **Outrun** — pairs well with synthwave for motion/speed/open-world tracks. *(Production: S3K project log)*
- **Liquid future bass** — produces floaty warmth without sidechain pump. Best for aquatic/atmospheric tracks. *(Production: S3K project log)*
- **Trap as co-genre** — adds weight and dread to atmospheric tracks *without* adding propulsion. Use for dark/restrained tracks where darksynth would be too aggressive. *(Production: ALttP Time of the Falling Rain; YI Underground BGM)*
- **Synthpop as co-genre** — adds brightness and pop character for cheerful/jaunty tracks. *(Production: YI project log)*
- **Kawaii + future bass** — irresistible groove combination for lighter/playful zones. *(Production: S3K Data Select session)*

---

## 6. Universal DNA Strings (Per Project)

### Sonic 3 & Knuckles (S3K) — LOCKED
```
synthwave, Sonic 3 & Knuckles faithful cover, propulsive forward momentum, melodic synth lead, bass and lead call and response, retro 16-bit harmonic DNA, groove-forward rhythm section, no vocals
```
**Album append:** `wide stereo field, atmospheric depth, dynamic range, cinematic production`
**In-game append:** `immediate energy, tight mix, punchy drums, seamless loop-ready, immediate energy from first second`

### Sonic the Hedgehog 1 — LOCKED
```
synthwave, Sonic the Hedgehog faithful cover, propulsive forward momentum, melodic synth lead, bass and lead call and response, retro 16-bit harmonic DNA, groove-forward rhythm section, no vocals, wide stereo field, atmospheric depth, dynamic range, cinematic production
```
*Note: Album-only project. Album append folded into core string. *(Production: Sonic 1 project log — corrected June 2026, replaces earlier draft string)*

### Sonic the Hedgehog 2 — PENDING
*Universal DNA string not yet built. To be constructed prior to generation phase.*

### A Link to the Past (ALttP) — LOCKED
```
synthwave, A Link to the Past faithful cover, propulsive forward momentum, melodic synth lead, bass and lead call and response, retro harmonic DNA, groove-forward rhythm section, no vocals
```
**Album append:** `wide stereo field, atmospheric depth, dynamic range, cinematic production`
**Guardrails (when needed):** `100% synthesized, analog synth, no real instruments, no live orchestra, no acoustic instruments`
**Immediate entry variant:** Replace `cinematic production` with `immediate energy from first second, hits hard from bar one, triumphant entry, no build-up, no intro swell, no slow start`

### Super Mario World (SMW) — LOCKED (v2)
Core DNA is structured rather than a single string. Every track begins from:
- **LEAD:** Full supersaw (inherited from ALttP project)
- **BASS:** Bouncy gated staccato bass (replaces ALttP sustain pads)
- **RHYTHM:** Future bass rhythmic sidechain + propulsive forward momentum, bass and lead call and response, no Latin percussion in A stems
- **TONALITY:** Bright major bias — dark/spooky tracks invert to minor (Ghost House, Valley of Bowser, Castle, Fortress → minor; swap bongos for industrial/mechanical perc; pull back supersaw brightness)
- **CALLBACK:** Thin square-wave counter-melody at -10 to -15 dB (SNES SPC700 ghost layer)
- **TEMPO:** 120–145 BPM target range

**Album append:** `wide stereo field, atmospheric depth, dynamic range, cinematic production`

### Yoshi's Island (YI) — LOCKED
```
synthwave, future bass, synthpop, Yoshi's Island faithful cover, 100% synthesized, analog synth, no real instruments, no live orchestra, no acoustic instruments, full supersaw lead, melodic synth lead, bass and lead call and response, playful chime and bell color, bouncy groove-forward rhythm section, retro 16-bit harmonic DNA, no vocals, wide stereo field, atmospheric depth, dynamic range, cinematic production
```

### Majora's Mask (MM) — LOCKED
```
synthwave, Majora's Mask faithful cover, propulsive forward momentum, melodic synth lead, bass and lead call and response, retro harmonic DNA, groove-forward rhythm section, no vocals
```
**Album append:** `wide stereo field, atmospheric depth, dynamic range, cinematic production`
**Guardrails (when needed):** `100% synthesized, analog synth, no real instruments, no live orchestra, no acoustic instruments`
**Immediate entry variant:** Replace `cinematic production` with `immediate energy from first second, hits hard from bar one, triumphant entry, no build-up, no intro swell, no slow start`
**Darksynth co-genre:** Standing addition for Ikana / endgame cluster.
**Giants vocal descriptor:** `deep choral voices, male choir, resonant, ceremonial, ancient vocal presence`

### Ocarina of Time (OoT) — PENDING
*Universal DNA string not yet established. To be built during first generation cluster.*

---

## 7. Generation Tips

### Reference Audio
- **Always upload a reference when possible** — anchors Suno to the correct melodic and tonal space far more reliably than prompt language alone. *(Production: S3K project log)*
- **Reference key overrides prompt key** — always analyze with `analyze_stem.py` before prompting. *(Production: S3K project log)*
- **Reference loudness affects output loudness** — quiet references (low LUFS) produce quieter stems. *(Production: S3K project log)*
- **Chiptune-style references are the strongest melodic anchor** when other arrangements cause Suno to improvise extra harmonies — the rigid transcription prevents melodic drift. *(Production: SMW Vanilla Dome session)*
- **Acoustic guitar references can produce unintended choral/vocal textures** on sacred-register tracks. Accept and use intentionally, or switch to a different reference type to avoid. *(Production: ALttP Safety in the Sanctuary)*
- **Guitar references + hybrid `[Disc_]` architecture = elevated bleed risk** — guitar timbre is amplified by the channel architecture. Use non-guitar references with hybrid, or add specific guitar negatives to `no_genre`. *(Live testing: June 2026 — Finding Set 11)*
- **Reference timbral character bleeds into output** — the arrangement style of your reference will influence the character of Suno's output. Choose references whose tonal character is compatible with your target sound.

### Generation Runs
- **Minimum 3 outputs per run** — Suno's variance between identical prompts is high. Single outputs are not evaluable. *(Established practice)*
- **Check the Exclude Styles field before every run** — Suno sometimes auto-tags uploaded references with conflicting genre descriptors. Clear them before generating. *(Production: SMW Vanilla Dome session)*
- **When prompt language consistently fails, pivot** — reframing to embrace Suno's natural tendency is more productive than continued refinement against the grain. *(Production: ALttP, SMW project logs)*
- **Optimal generation window:** 02:00–07:00 CET (8pm–1am ET) per controlled community testing. *(Reddit: r/SunoAI v5.5 study — Finding Set 1)*

### BPM Detection
- `analyze_stem.py` sometimes detects half-time BPM. If detected BPM feels slow, true tempo is likely 2×. *(Production: S3K project log)*
- **BPM prompt drift:** Suno often lands above or below the target BPM. If aiming for a specific tempo, prompt slightly above target. *(Production: S3K project log)*
- **Explicit numeric BPM in prompt produces tight tempo consistency** — confirmed YI Flower Field BGM (all 4 stems at exactly 117.45 BPM after adding "117 BPM" to prompt). *(Production: YI project log)*
- **3:2 BPM detection artifact** — if a single stem in a batch returns a BPM that's a clean 1.5× multiple of its batch-mates, treat as detection artifact rather than real tempo difference. *(Production: YI project log)*
- **Half-time BPM prompting can produce full-time results** — prompting a half-time target tempo (e.g. "83 BPM") produced stems at the full-time equivalent (172.27 BPM ≈ 2× target) consistent across an entire 8-stem batch. If targeting a specific tempo, verify whether Suno is interpreting it at face value or doubling it, and adjust the prompted number accordingly. *(Production: YI Castles & Forts BGM)*

---

## 8. Cross-Project Prompt Learnings

Key discoveries that apply across multiple projects, pulled from S3K, ALttP, SMW, MM, OoT, and YI production documents.

### Melodic Fidelity
- **"Faithful cover" in every prompt** — single biggest accuracy improvement. Non-negotiable. *(Production: S3K project log)*
- **Chiptune references lock melodic fidelity** — when guitar/piano covers cause Suno to improvise extra harmonies, switch to chiptune. The rigid transcription prevents drift. *(Production: SMW Vanilla Dome session)*
- **Reference audio dominates key** — prompt key specification is advisory, not binding. Reference key wins. *(Production: S3K project log)*
- **Mood/character descriptors shift mix balance** — "gentle/breezy/chime"-type descriptors measurably reduce bass-heaviness independent of tempo. Useful lever for tracks that need to feel lighter. *(Production: YI project log)*

### Vocals & Texture
- **Lyrics field vocalizes anything** — even short fragments, even with "no vocals" in style field. Bare markers only. *(Production: S3K, ALttP project logs)*
- **"No vocals" doesn't block choral texture** — on sacred/ceremonial tracks with classical guitar references, Suno may add choral "ahhh" textures. Accept or avoid by switching reference type. *(Production: ALttP Safety in the Sanctuary)*
- **Suno cannot hold conflicting emotions** — pick one per prompt; blend in DAW. *(Production: ALttP project log)*

### Structure & Production
- **Cinematic production descriptor causes slow openings** — remove for tracks requiring an immediate energy hit. *(Production: ALttP project log)*
- **Supersaw pads outperform "synthetic orchestra" language** for kinetic tracks. *(Production: ALttP Hyrule Field session)*
- **Trap as co-genre adds weight without aggression** — useful for atmospheric/dread tracks where darksynth would be too dominant. *(Production: ALttP Time of the Falling Rain; YI Underground BGM)*
- **Darksynth as supporting third co-genre** — adds menace to dark/minor tracks without overtaking the arrangement. Keep it listed third so it seasons rather than dominates. *(Production: YI Castles & Forts BGM; ALttP project log)*
- **Trap fails for chill/atmospheric tracks** — trap's inherent drive and punch conflicts with meandering/atmospheric character descriptors. Generate trap percussion separately in DAW if needed. *(Production: YI Underground BGM)*
- **"Clean" as positive descriptor neuters 808 weight** — if 808 punch is the goal, put cleanliness in the anti-prompt instead. *(Production: YI Underground BGM)*
- **Intra-zone tempo shifts** are achievable in DAW for album versions. *(Production: S3K project log)*
- **Cross-zone layering** — always check key compatibility before layering stems from different zones. *(Production: S3K project log)*
- **"Cinematic production" is incompatible with short stingers** — the descriptor implies breadth and development that works against brevity. Reserve it for full-length tracks only. For stingers/jingles/fanfares, use "wide stereo field" as the closing descriptor instead and let the `no_structure` anti-prompt block do the brevity work. *(Production: YI project log — retroactive fix across Tracks 1, 6, 15, 16)*
- **Reference orchestral character can pull toward an unexpected key** — an orchestral reference may consistently pull stems toward a different key than other references for the same track, independent of the source's actual key. Worth noting in track research if using a notably more "produced" reference than others in the batch. *(Production: YI project log)*

### SMW-Specific: Yoshi Layer Protocol
- All Yoshi-rideable level tracks require two stem passes: Variant A (clean, no Latin percussion) and Variant B (percussion-only bongo layer). *(Production: SMW project log)*
- **Variant B prompt:** `pure Latin percussion, synthetic bongos as lead voice, future bass rhythm, Afro-Cuban bongo pattern at [BPM], rim shots/shaker/hi-hat locked with kick and snare, no melody/chords/bass/harmonic content` *(Production: SMW project log)*
- Future bass descriptor adds measurable low-mid weight to the bongo groove. *(Production: SMW project log)*
- Map screen tracks do **not** get a Yoshi bongo pass. Ghost Houses and Castles: no Yoshi pass. *(Production: SMW project log)*
- **This protocol is SMW-specific and does not transfer to other Yoshi-featuring projects.** It models a specific audio element (a mounting-triggered percussion layer) present in SMW, Sunshine, Galaxy 2, and NSMB Wii/U. Confirmed not applicable to Yoshi's Island, where Yoshi is the player character from the start with no mounting mechanic and no corresponding musical layer in the source OST. *(Production: YI project log)*

### OoT/MM-Specific: Ocarina Timbral Considerations
- The ocarina instrument timbre causes Suno to generate in a very specific sonic register — the character itself shapes the output strongly regardless of other prompt language. *(Production: OoT, MM project logs)*
- Alternative reference arrangements with different lead instruments (orchestrated, fingerstyle guitar, nylon guitar, flute covers, piano) produce more neutral, mixable output. *(Production: OoT, MM project logs)*
- OoT carry-overs into MM (19 tracks): reference pool established in OoT phase can be reused directly. *(Production: MM project log)*

### MM-Specific: Giants Voices
- Deep choral "Call us" vocal texture is a distinctive MM identity element. *(Production: MM project log)*
- Prompt descriptors: `deep choral voices, male choir, resonant, ceremonial, ancient vocal presence` *(Production: MM project log)*

### ALttP-Specific
- **Acoustic guitar timing bleeds into output** — classical guitar references with rubato/micro-timing produce looseness in stems. Accept and correct in DAW. *(Production: ALttP Beginning of the Journey)*
- **Light World / Dark World duality is a design pillar** — should be audible across the album. *(Production: ALttP project log)*
- **Duplicate-melody tracks** must be prompted differently based on in-game context and emotional register, not just melody. *(Production: ALttP project log)*

---

## 9. Reddit Findings Log

A running log of community posts evaluated for pipeline applicability. All findings are attributed to their original authors.

---

### Finding Set 1 — Structured Prompts & Categorized Anti-Prompts
**Source:** r/SunoAI (Suno v5.5 study) | **Evaluated:** Early June 2026

**Claims:**
- Structured key:value format outperformed flat tag lists (4/11 → 10/11 criteria met)
- Categorized anti-prompts outperform flat NO lists
- Optimal generation window: 02:00–07:00 CET
- 2–3 complementary genre tags outperform single tags

**Adopted:**
- Categorized anti-prompt format (`no_structure`, `no_genre`, `no_mix`, `no_master`, `no_vocal`) ✅
- Generation timing guidance ✅
- Multi-genre stacking + dominant-first ordering ✅

**Rejected:**
- Full key:value structure in style field — A/B tested on Green Hill Zone, no measurable quality difference vs. flat format. Flat retained for character budget. ❌

---

### Finding Set 2 — Structure Fixes (3-Part Guide)
**Source:** u/sunoarchitect, r/SunoAI | **Evaluated:** June 2026

**Fix 1 — `[End]` tag is mandatory**
Without it, Suno generates past the natural finish or cuts awkwardly at 4 minutes.
**Adopted:** Added `[End]` as final line of standard lyrics field template ✅

**Fix 2 — Syllable consistency within ±2 per line across verses**
Suno uses syllable density as a rhythmic anchor. Inconsistency causes random rhythm generation.
**Adopted:** N/A — instrumental pipeline, no lyric content in lyrics field ⬜

**Fix 3 — Meta tags BEFORE section header, not after**
Suno reads top-to-bottom and commits to generating a section on hitting the header. Tags placed after arrive too late.
**Noted:** We don't use inline meta tags currently, but principle applies if we ever do ✅

**Bonus finding — Genre tag order matters**
Dominant genre first. Order shapes the output's center of gravity.
**Adopted:** Codified as style field rule ✅

---

### Finding Set 3 — Emotion Switching Per Section
**Source:** u/Grenar, r/SunoAI ("Day #13 Dirty Tricks — Trick #16") | **Evaluated:** June 2026

**Claim:** Assign different emotion tags inside section headers in the lyrics field to bias melody shape, intensity, harmonic tension, and pacing per section. Key insight: "Emotion only works when it **changes**. Contrast is everything."

**Emotion → Suno behavior mapping (reference table):**

| Tag | Suno Response |
|---|---|
| Sad | Minor key, slower, sparse |
| Angry | Aggressive voice, faster, louder |
| Desperate | Sparse instruments, raw vocals |
| Explosive | Full band, maximum energy |
| Melancholic | Slower tempo, minor harmonies |
| Euphoric | Faster, major key, bright |
| Defiant | Strong delivery, powerful |
| Reflective | Gentle, thoughtful pacing |
| Triumphant | Full arrangement, victorious |

**Fails when:** emotions contradict tempo, no contrast between sections, changes too frequent, labels too similar.

**Adopted:** Not directly applicable — our lyrics field uses `[Disc_]` channel headers + bare markers; emotion tags in section headers would trigger vocalization. ⬜

**Extracted principle:** The emotion→behavior table is useful as a **style field descriptor guide**. If a track needs to feel "triumphant," prompt `full arrangement, victorious` in the style field. These are the Suno-side translations of emotional registers.

**Also reinforces:** Suno can't hold conflicting emotions — contrast between sections is the mechanism, handled in the DAW rather than the prompt. ✅

---

### Finding Set 4 — Describe Absence, Name Instruments Specifically
**Source:** u/BryanP7737, r/SunoAI ("The reason Suno turns your South African 3-step into generic Afro house") | **Evaluated:** June 2026

**Core finding:** Genre labels fail when the genre's identity comes from something *removed* rather than added. Suno fills every beat by default. To get a withheld beat, space, or gap, you must describe the absence explicitly.

**Three fixes:**
1. Spell out the kick pattern mechanically — not the genre name, but the actual rhythm structure including what's missing
2. Name specific instruments — "log drum" not "percussion." Generic instrument categories evaporate into synth bass
3. Ask for swing and space explicitly — "shakers slightly late, room between hits." Suno defaults to tight quantized wall

**Adopted:**
- **Describe what's absent** principle added to anti-prompt rules ✅
- **Name instruments specifically** principle added to style field rules ✅
- **Describe rhythm mechanically** principle added to style field rules ✅

---

### Finding Set 5 — Intro Hum Blocklist & Texture Language
**Source:** u/PureRely (intro hums, 58 upvotes) + u/SevenSky_Tech1 (v5.5 prompts list), r/SunoAI | **Evaluated:** June 2026

**u/PureRely — Intro hum fix:**
Suno frequently opens tracks with wordless humming/vocalizing. Full blocklist added to Section 4.
**Adopted:** Intro hum blocklist added to anti-prompt section ✅

**u/SevenSky_Tech1 — Three v5.5 tricks:**
1. `[Hyper-Realistic]` quality tag — claimed to force best audio engine. **Untested by us.**
2. Texture language over instrument names — already our practice, now explicitly codified ✅
3. "My Taste" magic wand — paid plan feature. **Not part of standard pipeline.**

**Prompt length warning:** Stick to 4–7 descriptive words per concept. Too much text confuses Suno. ✅

---

### Finding Set 6 — Vocal Register Control Per Section
**Source:** u/Grenar, r/SunoAI ("Day #12 Dirty Tricks — Trick #15") | **Evaluated:** June 2026

**Claim:** Assign vocal register tags inside section headers to bias pitch range, breathiness, and vocal effort per section.

**Register → character mapping:**

| Register | Character | Best For |
|---|---|---|
| Falsetto | Light, airy, high, head voice | Ethereal moments |
| Chest Voice | Full, powerful, grounded | Confident delivery |
| Head Voice | Clear, light, high but full | Pop brightness |
| Mixed Voice | Balanced, versatile | Modern pop/R&B |
| Whispered | Soft, intimate | Secret, vulnerable moments |
| Spoken Word | Narrative, non-melodic | Rap verses, intros |
| Raspy | Hoarse, gritty | Rock, blues, raw emotion |
| Belted | Maximum power, full volume | Climax moments |

**Adopted:** Not applicable to current instrumental pipeline. ⬜
**Logged for future reference:** If vocals are ever added to a project, this is the mechanism for controlling vocal character per section.

---

### Finding Set 7 — Lyrics Field Channel Architecture ("Disc_" Syntax)
**Source:** u/Zestyclose_Basil_349, r/SunoAI ("Lyrics prompt is All You Need") | **Evaluated & Tested:** June 2026 | 76 upvotes, 411 shares

**Core claim:** The lyrics field has a fundamentally different and more powerful relationship with the generation engine than the style field. You can define sonic channels directly in a header block at the top of the lyrics field using bracket syntax.

**Format:**
```
[Disc_Drums: descriptor | descriptor | spatial_position]
[Disc_Sub: descriptor | descriptor | Mono_Sub_Lock]
[Disc_Pad: descriptor | descriptor | Stereo_Width_Maximum]
[Disc_Vocal: descriptor | descriptor | Center_Front]

[Verse]
[Chorus]
[Outro]
[End]
```

**Why it works:** Each token is an address into the training data. The model activates the neighborhood around that address and applies it to that frequency zone. Specific vocabulary loads very precise behavior. You are not describing what you want — you are addressing where it already lives in the training data.

**Three sub-techniques from original post:**
1. **Instrument backdoor** — naming specific hardware loads its entire cultural/timbral world automatically
2. **Guitar pedals as timbral modifiers** — guitar processing vocabulary applied to non-guitar channels applies the processing character without summoning a guitar
3. **Vocal processing on non-vocal channels** — vocoder, telephone bandpass, Wall of Sound compression applied to instrument channels

**LIVE TEST RESULTS — June 2026**

**Test 1 — Channel headers (GHZ, underscore syntax)**
Result: No vocalization ✅ Critical gate confirmed open.

**Test 2 — Contrast Pass A (dark/industrial channels, style field = `synthwave`)**
Result: Output landed noticeably darker and heavier ✅

**Test 3 — Contrast Pass B (chillwave/ambient channels, style field = `synthwave`)**
Result: Output landed softer and dreamier ✅

**Test 4 — Instrument backdoor (Steel_Drum on lead, Minimoog_Analog_Pad on pads)**
Result: Neither instrument audibly present. Steel drum too far outside synthwave training neighborhood. ❌

**Test 5 — Hybrid (full style field DNA + Disc_ headers)**
Result: Sparkle arpeggio not improved. Subjective impression of richer, more layered output noted consistently across multiple tracks and sessions. ✅ (pending blind test confirmation)

**Conclusions:**
- `[Disc_]` syntax is safe — no vocalization ✅
- Strong for macro timbral direction ✅
- Weak for specific instrument summoning ❌
- `[Disc_No]` channel for negatives does not work — keep negatives in style field ❌
- Hybrid (full style field + Disc_ channels) adopted as new baseline ✅

**Untested — worth future exploration:**
- Electronic hardware names (Linn LM-1, Minimoog, Mellotron, Buchla) in Disc_ channels
- Guitar pedal processing vocabulary on synth channels

---

### Finding Set 8 — Performance Prompting in the Lyrics Field
**Source:** u/QuellMusik, r/SunoAI ("To make interesting music learn how to performance prompt in lyrics!") | **Evaluated:** June 2026

**Core argument:** Style field sets the world (genre, foundation, mood). Lyrics field directs the performance — section by section, moment by moment.

**Method:** Inline bracketed performance directions embedded throughout the lyrics field, interspersed with lyric content.

**Generation settings noted by author:**
- Weirdness: 50–75%, Style influence: 100%, Audio influence: 25%
- Generate in v4.5 first, remaster into v5.0 three times at subtle/normal/high settings
- Listen to all six versions, pick best

**Pipeline applicability — UNTESTED:**
Bracketed inline directions use natural language — based on our confirmed finding that natural language in the lyrics field gets vocalized, these directions would likely be sung. This approach is designed for vocal song production, not instrumental.

**Extracted principle:** Style field = the world. Lyrics field = the performance direction. Consistent with how we already use both fields. ✅

---

### Finding Set 9 — Generation Settings Workflow
**Source:** u/QuellMusik, r/SunoAI | **Evaluated:** June 2026

**Workflow:** Generate in v4.5 → remaster into v5.0 at subtle/normal/high → listen to all six versions → select best → master with external tool.

**Slider settings:** Weirdness 50–75%, Style influence 100%, Audio influence 25%.

**Status: Untested.** ⏳

---

### Finding Set 10 — Tag Stacking with Pipe Symbol
**Source:** u/Grenar, r/SunoAI ("Day #8 Dirty Tricks — Trick #11") | **Evaluated & Tested:** June 2026

**Core finding:** The pipe symbol `|` separates instructions cleanly inside bracket tags. Without it, comma-separated adjectives blur together. With pipes, each element is processed as a discrete instruction. Max 7 elements per tag.

**Optimal element order:**
`[Section | Mood/Energy | Vocal Style | Key Instruments | Dynamic/Movement | Spatial/Effects | Production Style]`

**LIVE TEST RESULTS — June 2026**

**Test A — Natural language pipe-delimited section headers**
Result: No vocalization ✅ / No meaningful steering effect ❌
Verdict: Non-vocalizing but not useful. Not adopted. Bare section markers remain standard.

**Test B — Pipe symbol in style field**
Result: No quality improvement ❌ / Guitar reference bleed amplified ❌
Verdict: Not adopted. Commas confirmed as correct standard for style field.

**Test C — Comma separation inside `[Disc_]` channels (in place of pipes)**
Tested on GHZ and SMW Underground.
Result on GHZ: Uncertain richness signal — "maybe something different" but not confidently richer than pipe version.
Result on SMW Underground: Confidently richer than baseline/control.
Guitar bleed present on both test tracks regardless of comma vs. pipe delimiter — see Finding Set 11 refined conclusion (bleed driven by prompt character, not delimiter choice).
Verdict: Mixed/inconclusive on richness. Pipes retained as standard for `[Disc_]` channel internals based on more consistent results across prior testing, but commas are not disqualified — worth revisiting with more samples.

**Broader implication — CORRECTED (see Finding Set 11):** The guitar bleed observed in this pipe-separated style field test was later traced to negative prompt over-specification, not the pipe delimiter or structured syntax itself. The style field used in this test carried an extensive guitar/rock negative list, which is now understood to be the actual cause. The comma-vs-pipe question for the style field remains separately answered (no quality improvement from pipes) but the bleed observation in this entry should not be read as evidence against pipe syntax specifically.

---

### Finding Set 11 — Guitar Bleed: Root Cause Identified (CORRECTED)
**Source:** Live testing, multiple sessions | **Discovered, Revised, and Resolved:** June 2026

**Summary of investigation:** This finding went through three stages of revision as testing isolated the actual cause. The final, confirmed root cause is documented below. Earlier theories (reference-type-specific, hybrid-architecture-specific, prompt-energy-specific) are preserved further down for transparency but are **superseded**.

**ROOT CAUSE — CONFIRMED:** Guitar/rock bleed is caused by **over-specified negative prompting**, not by the reference, not by the `[Disc_]` channel architecture, and not by dark/driving prompt character alone. Naming multiple related guitar/rock tokens in `no_genre` (e.g. `rock, metal, hard rock, alternative rock, distorted guitar, electric guitar lead, power chords, guitar riff, guitar solo, overdriven guitar`) appears to activate the associated neighborhood in Suno's model rather than suppress it — the opposite of the intended effect.

**Isolation test sequence (SMW Underground, dark Dorian, driving trap-synthwave):**

| Test | Negative List | `[Disc_]` Channels | Result |
|---|---|---|---|
| Original generation phase | Minimal (`darksynth` only, approx.) | N/A — pre-hybrid | Guitar minimal, baseline acceptable |
| Reinforced hybrid | 8 guitar/rock tokens in `no_genre` | Present | Heavy guitar, 3/3 outputs |
| Reinforced baseline | 8 guitar/rock tokens in `no_genre` | Absent | Heavy guitar, 3/4 outputs |
| Reinforced positives, minimal negative (`darksynth` only) | 1 token | Absent | **Minimal/no guitar, 4/4 outputs** |
| Reinforced positives, minimal negative + Disc_ channels restored | 1 token | Present | **Minimal/no guitar, 4/4 outputs** — plus added melodic/textural richness |

**Conclusion:** Stripping the guitar/rock-specific tokens from `no_genre` down to a single broad term (`darksynth`) eliminated the bleed entirely, regardless of whether `[Disc_]` channels were present. Restoring the channels afterward — with the minimal negative list — produced clean output *and* the richness/textural improvement previously associated with the hybrid architecture, with no bleed cost.

**This means:**
- The `[Disc_]` channel architecture is **not** a bleed risk on its own — fully exonerated
- Reference type (guitar vs. non-guitar) was **not** the deciding factor — both guitar and non-guitar references showed bleed under heavy negative lists in earlier (now superseded) tests
- Dark/driving prompt character alone does **not** guarantee bleed — it only became a problem when paired with an over-specified negative list
- **Negative prompt scope has a ceiling.** Long, specific exclusion lists — especially for tokens with strong genre/instrument associations — can paradoxically increase the presence of the excluded element

**Follow-up isolation test — which factor actually did the work?**

The winning test changed two things simultaneously: (1) stripped `no_genre` to a single token, and (2) added synth-specific positive language (`100% synthesized`, `sequenced synth bass driving the rhythm`, etc.) in place of generic energy language. A follow-up test held the minimal negative list constant and reverted the positive language back to generic (`propulsive forward momentum`, `bass and lead call and response`, `groove-forward rhythm section`) with no synthesized guardrail.

**Result: 4/4 clean, no guitar bleed** — confirming the negative list reduction alone is sufficient to prevent bleed. The positive synth-specific language was not necessary for bleed prevention.

**However, a real qualitative difference was noted between the two clean results:** with generic positive language, the output was described as "a synth that bordered on being a guitar" — clean of literal guitar, but with a character that leaned toward guitar-adjacent timbre, and noted as "more aggressive" in character. With the synth-specific positive language, the result was more confidently and cleanly synthetic, with less of that aggressive edge.

**Two-factor model (revised):**
1. **Negative list scope = the bleed-prevention mechanism.** This is the necessary and sufficient factor for preventing literal guitar bleed.
2. **Positive synth-specific language = a separate, independent timbral-refinement lever.** Doesn't control bleed, but pulls the synth character further from guitar-adjacent territory and tempers aggressive edge once bleed is already controlled by the negative list.

These are two distinct levers, not one combined effect. Use minimal negatives to prevent bleed; layer in synth-specific positive language afterward for additional timbral polish if the result still feels guitar-adjacent in character even without literal guitar presence.

**Generalization test — does this extend beyond guitar/rock?**

Tested on a maximally different track to check whether the over-specification backfire is guitar-specific or a general property of Suno's negative prompting. YI Athletic BGM is a bright, major-key, synthpop-adjacent track — tonal opposite of SMW Underground's dark Dorian character.

| Test | `no_genre` | Result |
|---|---|---|
| Heavy list | `trap, darksynth, dark ambient, minor key drift, dubstep, drill, phonk, horrorcore, industrial, dark techno` | Explicit dubstep elements present, more aggressive synth character throughout, likely other named dark-genre textures bleeding in unidentified by ear |
| Minimal list | `darksynth` only | 4/4 clean — bright and happy throughout, no dark/aggressive bleed, matches original control character |

**Confirmed: the mechanism generalizes.** This is not a guitar/rock-specific quirk — it held across two completely different stylistic axes (dark/rock-guitar on a dark track, and dark/aggressive-electronic on a bright track). **Negative list over-specification is a general property of how Suno processes exclusions**, not a one-off.

**Notable side observation:** the "backfired" heavy-negative-list output on Athletic BGM was independently judged as a genuinely interesting creative result — a bright, happy track with unexpected dubstep texture woven through it, described as a juxtaposition that "worked well."

**Follow-up: can this be deliberately exploited as an intentional technique?**

Tested whether naming a single target genre in the negative list could reliably introduce its texture on command — i.e., using `no_genre` as a backdoor for requesting a flavor rather than excluding it. Tested on ALttP The Goddess Appears (sacred/ethereal/hymnal track, maximally opposed in character to dubstep) using the Meeting the Maidens chiptune reference (the documented winning reference for this track).

| Test | `no_genre` content | Token count | Position of `dubstep` | Result |
|---|---|---|---|---|
| Single token | `dubstep` | 1 | 1 of 1 | **Clean — 4/4 light and angelic, no dubstep present** |
| Short list | `trap, darksynth, dark ambient, minor key drift, dubstep` | 5 | 5 of 5 (last) | **Dubstep elements present** from track 1 onward |
| Original heavy list (Athletic BGM, reference) | 10 tokens, dubstep mid-list | 10 | 5 of 10 | Dubstep elements present |

**Conclusion: the intentional-injection theory does NOT hold.** A single named token — regardless of which genre — suppresses reliably, exactly as intended. Naming `dubstep` alone did not summon dubstep; it blocked it. The bleed only reappears once the category grows to roughly 5 or more tokens, **regardless of where the target genre sits in the list** (last position in the 5-token test bled just as readily as mid-list position in the 10-token test).

**This refines the root-cause model further:**
- **Count is the dominant factor, not position.** Earlier hypotheses about list-order weighting (relevant for positive `[Disc_]` channels) do not appear to govern negative-category bleed risk the same way.
- **The mechanism is not genre-specific resonance.** It is not that certain genres are inherently "louder" or more prone to bleeding through — it's purely a function of how many competing tokens occupy the same negative category.
- **Single-token negative categories are reliably safe** at n=1, confirmed across darksynth, dubstep, and other tested genres.
- **The threshold for risk sits somewhere at or before 5 tokens** — confirmed bleed at exactly 5, confirmed clean at 1. Exact threshold between 2–4 tokens not yet tested.
- **This is not a usable technique for deliberate genre injection.** The Athletic BGM "happy accident" was a byproduct of list crowding, not a controllable backdoor. Do not rely on negative-list naming to intentionally introduce a flavor — if a genre's texture is wanted, prompt for it positively instead.

**Creative note preserved for future use (not a tested finding, an idea worth logging):** the aggressive textural quality that read as "dubstep" on Athletic BGM may be a worthwhile deliberate layering ingredient for tracks already leaning into power rock, darksynth, or trap territory — i.e., not as a hidden technique, but as a positive-prompt flavor worth testing directly in projects calling for that kind of aggressive edge.

**Revised practical rule (REPLACES all prior guidance in this Finding Set):**
- Keep `no_genre` and other negative categories **short and broad** rather than long and specific, especially for tracks with dark/driving character where rock-adjacent associations are already a known risk
- Prefer a single broad exclusion (`darksynth`) over an exhaustive list (`distorted guitar, electric guitar lead, power chords, guitar riff...`) when trying to steer away from a stylistic neighborhood
- **This rule applies generally, not just to guitar/rock** — confirmed across multiple unrelated stylistic axes (dark/rock, dark/aggressive-electronic, sacred/dubstep). Treat any long, specific negative list as a risk regardless of which genre family it targets
- **Risk is driven by token count in the category, not position within the list and not which specific genre is named.** Single-token categories are safe; categories at or above ~5 tokens carry confirmed bleed risk
- If bleed appears, **try removing negative tokens before adding more** — the instinct to over-specify the exclusion is often counterproductive
- **Minimal negatives prevent literal bleed; synth-specific positive language refines timbral character beyond that.** Apply both as separate, independent levers rather than assuming one does the other's job
- The `[Disc_]` hybrid architecture remains the recommended baseline — it adds richness with no confirmed bleed cost once the negative list is kept minimal
- Always check whether bleed was already present in baseline/original generation-phase results before attributing it to a new technique — earlier conclusions in this Finding Set initially misattributed a pre-existing issue to the hybrid architecture before the negative-list cause was isolated

---

### Finding Set 12 — Suno Architecture & Compression Mechanics (External Research)
**Sources:** Julian Mateo, "How Suno Prompt Works: 2026 Guide to Music Generation," apipass.dev, May 26, 2026; Julian Mateo, "Suno V5 & V5.5 Prompt Hacks with Templates (2026)," apipass.dev, May 26, 2026. Both articles cite community researcher **AnytimeSyS** (Reddit, r/SunoAI) as the original source of the compression-mechanics findings. | **Evaluated:** June 2026

**Note on sourcing:** These are third-party explainer articles, not official Suno documentation — Suno has no official public prompt documentation as of this writing. Claims below are presented as reported, not independently verified by us except where cross-referenced against our own live testing.

**Core architectural claim:** Suno processes prompts through a Language Model Layer (LML) that extracts high-level musical attributes, then a Music Transformer Model (MTM) that handles compositional logic, before passing a single compressed latent vector to the audio diffusion stage. Text functions as a *condition signal*, not an instruction set — unlike how LLMs process language.

**The compression problem (originally documented by AnytimeSyS, r/SunoAI):** Long, detailed prompts get compressed into a small latent vector dominated by the most "tag-like" high-frequency training concepts. Extra descriptive detail is frequently averaged out or lost entirely. A 200-word prompt and a 10-word prompt can produce nearly identical output if they share the same dominant genre/style tags.

**This directly corroborates our Finding Set 11 conclusion from an architectural angle.** Our live-tested discovery that long `no_genre` lists backfire is consistent with this compression model: each named token pulls the compressed vector toward its own neighborhood, and with multiple competing tokens, the averaging process can let one through rather than excluding it. This gives our empirical finding a plausible mechanical explanation rather than leaving it as an unexplained correlation.

**Claimed extraction priority order (LML attribute weighting):**
1. Genre / style family (highest weight)
2. Instrumentation (named instruments map directly to training tags)
3. Tempo and energy
4. Emotional character / mood (lower precision — survives compression but influences output less specifically)
5. Vocal direction (directive tags outperform descriptive phrases here)
6. Structural intent (section tags function as directives)
7. Contextual/atmospheric descriptors (lowest weight — first to be compressed out)

**High-signal vs. low-signal vocabulary:** Genre labels, instrument names, technical descriptors (BPM, key), and directive bracket tags are claimed to survive compression reliably. Emotional metaphors, generic quality adjectives ("beautiful," "epic"), and abstract sensory language are claimed to be unreliable and to consume "prompt budget" without proportionate effect.

**Square brackets `[ ]` vs. parentheses `( )` — claimed as two distinct mechanisms:**
- Square brackets are described as directive commands operating at a deeper architectural level — `[Instrumental]`, `[No Vocals]`, `[Verse]`, `[Chorus]`, `[Minimal Variation]`, `[Sustained]`, `[Hyper-Realistic]`
- Parentheses are described as soft descriptors that pass through normal compression as supplementary nuance — e.g. `[Chorus] (add harmonies, bigger drums, push the hook)`

**Specific directive tags claimed (UNTESTED BY US except where noted):**
- `[Instrumental]` — claimed to suppress vocal generation at the composition/architectural level, distinct from `[No Vocals]` which operates at output level. The "double lock" pattern places `[Instrumental]` at the very start of the prompt and `[No Vocals]` at the very end. **We currently do not use `[Instrumental]` in our pipeline — worth testing.**
- `[Hyper-Realistic]` — claimed to force 48kHz audio diffusion output mode, placed first before all other tags. Already logged as untested in Finding Set 5 from a separate Reddit source; this article corroborates it with more architectural framing.
- `[Minimal Variation]` — claimed to lock texture for loopable content, suppressing Suno's default song-arc structure.
- `[Sustained]` — claimed to go further than `[Minimal Variation]`, signaling pure atmospheric continuity with no structural development. Relevant primarily for ambient/drone use cases, lower priority for our narrative-driven album tracks.

**The "signal chain" style field structure (claimed validated for V5.5 precision control):**
```
{genre}, {drum description} → {bass description} → {harmonic layer} → {melodic element} → {FX/texture}, {BPM}, {key}
```
Structuring the *style field* as a signal chain (drums → bass → harmony → melody → FX → mix) using arrow delimiters is claimed to outperform loose adjective lists for controlling sonic character. This is conceptually adjacent to our `[Disc_]` channel architecture but differs in two ways: it lives in the **style field** rather than the lyrics field, and it uses arrow (`→`) delimiters rather than pipes. **Untested by us — worth comparing directly against our hybrid approach.**

**Co-occurrence cluster tags (claimed, unverified by us):** Certain tags are claimed to activate learned clusters of associated production characteristics beyond their literal meaning — e.g. "Bedroom pop" reportedly activates reverb-heavy lo-fi vocal production by association. Presented as emergent/undocumented behavior discovered through community testing rather than official features. Not yet relevant to our instrumental pipeline but worth keeping in mind.

**Practical principles claimed by the source (cross-reference against our existing rules):**
- Lead with the dominant/most important signal first — **consistent with our existing genre-order and channel-order rules** ✅
- Limit genre stacking to 2–3 — **consistent with our existing genre tag rules** ✅
- Limit core named instruments to 2–3 anchors — **partially consistent**; our `[Disc_]` channels typically use more descriptors per channel than this suggests, worth monitoring
- Change one variable at a time when iterating — **consistent with our testing methodology throughout this document** ✅

**Status: Partially corroborated, partially untested.** The compression-mechanics explanation aligns well with and helps explain our own live-tested Finding Set 11 results. The specific directive tags (`[Instrumental]`, signal-chain style field structure) are new to our pipeline and untested — flagged for future testing sessions.

---

### Finding Set 13 — Signal Chain Style Field: Tested, Valuable, But Does Not Stack With Disc_ Channels
**Source:** Live testing, GHZ session, following Finding Set 12 external research | **Tested:** June 2026

**Technique tested:** Arrow-delimited signal chain structure in the **style field** (`{drum} → {bass} → {harmony} → {melody} → {mix}`), as described in the apipass.dev articles (Finding Set 12), tested standalone and in combination with our established `[Disc_]` channel architecture.

**Test sequence (GHZ, classical guitar reference, minimal `no_genre: [darksynth]` throughout):**

| Test | Style Field | Lyrics Field | Result |
|---|---|---|---|
| Signal chain alone | Arrow-delimited (5 stages) | Bare markers, no Disc_ | Distinct quality noted — "doing something, I like it." Consistent across batch. Minimal guitar bleed (expected, reference-driven). |
| Standard hybrid (baseline) | Comma-separated, standard order | `[Disc_]` channels present | Familiar richness/depth quality, as established in Finding Set 7/11. Consistent across batch. |
| **Combined** (signal chain + Disc_ channels) | Arrow-delimited | `[Disc_]` channels present | **Inconsistent — 2 of 4 outputs rich/full/developed as expected; 2 of 4 noticeably "anemic," wildly different in character from the other two within the same batch.** |

**Key observation:** Both standalone techniques (signal chain alone, hybrid alone) produced **consistent** results across their respective batches. The combined version produced a **50/50 split** within a single batch — a level of inconsistency neither standalone technique showed. This was confirmed as a real effect, not ordinary batch variance, based on direct comparison to the standalone batches' consistency.

**Interpretation:** Per the compression model described in Finding Set 12, both the arrow-delimited signal chain and the `[Disc_]` channel headers function as dense, independently-structured instruction blocks. Stacking two such systems simultaneously appears to create competition within Suno's compressed latent representation — sometimes resolving cleanly (rich outputs), sometimes not (anemic outputs) — rather than the two systems' benefits adding together reliably.

**Conclusion:**
- **Signal chain style field structure is a genuinely valuable standalone technique** — distinct positive character noted, independent from what `[Disc_]` channels provide, consistent results
- **`[Disc_]` channel hybrid remains independently valuable** — established richness/depth quality, consistent results
- **The two techniques should NOT be combined as standard practice** — stacking them trades batch consistency for occasional (unreliable) extra richness, which conflicts with our core methodology of trusting batch consistency (minimum 3–4 outputs per run specifically to evaluate reliability)

**Standing decision:** The **hybrid approach (comma-separated style field + `[Disc_]` channels in lyrics field) remains the standard baseline**, now formally folded together with the minimal-negative-list rule (Finding Set 11) as confirmed standard practice. The arrow-delimited signal chain is logged as a **viable alternative technique** worth deploying on its own (style field only, bare lyrics markers) for tracks where the standard hybrid isn't landing the desired character — but not as an addition layered on top of the hybrid.

---

*To update this document: upload it to a new chat and request additions/changes by section.*
