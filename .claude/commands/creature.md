You are helping the DM build a new beastiary entry for The Wheat Sea campaign. This is a logistics/formatting task — you will structure the DM's creative decisions into the correct JSON schema. Do not invent lore, names, flavor text, or mechanical choices. Ask, then record.

## Authoritative sources — read these first

The schema is **not** maintained in this command. It lives in the data files. Before proposing anything, read:

- **`GM_notes/Beastiary/data/component_library.json`** — the authoritative source for reusable stat-block pieces. Entries are built **from** it, never the reverse:
  - `schema` — the field contract (per-field `note`, `options`, `required`).
  - `templates` — blank per-archetype skeletons (`individual`, `named_npc`, `mob`, `creature`, `endemic_life`).
  - `tables` — canonical roll tables (`minor_variants_humanoid`, `major_variants_humanoid`, `equipment_humanoid`, `weapons_mob`, `variants_mob`). When the DM wants a standard table, **copy the matching one verbatim** rather than re-authoring it; only deviate for entries the DM explicitly customizes.

  Field names here match `beastiary.json` exactly.
- **`GM_notes/Beastiary/data/action_library.json`** — reusable named attacks, traits, conditions, and villain-action patterns pulled from existing entries. Offer these to the DM instead of authoring from scratch where one fits.
- **`GM_notes/Beastiary/data/beastiary.json`** — existing entries; match their formatting exactly.

If a field question below is ever unclear, the `schema` section of `component_library.json` (with its per-field `note`, `options`, and `required` flags) is the source of truth.

## Phase 1: Gather Information

Enter plan mode and ask the DM the questions below in a single message. Wait for answers before proceeding.

First, determine the **archetype**, because it selects the template and which questions matter:
- **individual** — a single statted combatant (most humanoids/monsters)
- **named_npc** — a named character with Villain Actions / Conditions
- **mob** — a grid-footprint swarm (uses `variants`/`weapons` tables, `actions_per_round`, `max_attacks_per_opponent`)
- **creature / endemic_life** — fauna and wildlife (adds `creature_class`, `physical_size`, `flavor`, `ecology`, `at_table`)

**Ask all of these at once:**

1. **Name & Role** — Name, short role descriptor (`role`, e.g. "Bandit Captain"), and combat role(s) (`roles`: Minion / Leader / Solo / Support / Elite — array).
2. **Type & Size** — `creature_type` (Humanoid, Beast, Undead, Fiend, etc.) and `size` (Tiny / Small / Medium / Large / Huge / Gargantuan). For mobs, include grid footprint, e.g. "Large (3x3x1)". For fauna/endemic, also ask `creature_class` (e.g. "Serpent", "Prey Animal", "Avian Wyvern") and `physical_size` (precise size/weight, real-world analogue welcome).
3. **Core Stats** — approximate `challenge` (CR), `armor_class`, `hit_points` (and `hit_dice` if known), `speed`, and `morale` (integer save threshold 1–20, or a string like "Does not flee" that bypasses saves).
4. **Ability Scores** — either the six scores directly, or a physical/mental profile and I will suggest scores for approval.
5. **Senses & Languages** — `senses` line (e.g. "Passive Perception 12, Darkvision 60 ft.") and `languages` line (e.g. "Cerik" or "—"). Optional; skip for mindless fauna.
6. **Combat Actions** — attacks/abilities used on its turn: weapon names, melee or ranged, `to_hit`, `reach`/`range`, damage, and special effects. (I will check `action_library.json` for reusable ones.)
7. **Special Mechanics** — yes/no/describe for each: bonus actions, triggered actions (non-reaction "when X" effects, e.g. morale saves), reactions, villain actions (named NPCs only; malice cost), conditions it applies/starts with, passive traits, part breaks (targetable anatomy for large/boss creatures).
8. **Variants & Equipment** — minor variant table (individual variation), major variant table (rare high-impact traits), and/or an equipment table. For mobs use the `variants`/`weapons` tables instead. For a standard humanoid/mob creature, ask whether to use the canonical `component_library.tables` entries as-is; only ask for custom entries where the DM wants to deviate.
9. **Manner** — (Optional) short behavioral disposition driving tactics (e.g. "Threatening Opportunism", "Pack Instinct").
10. **Fauna/endemic only** — `flavor` (description + appearance prose), `ecology` (environment terrain types, habitat, indicator, utility), and `at_table` (sensory presentation, behavioral tells, ecology/habitat notes). These add the at-table depth expected of GM_notes fauna entries.

Once you have the DM's answers, exit plan mode.

## Phase 2: Propose the Statblock

Start from the matching blank template in `component_library.json` `templates` and fill it with the DM's answers. Rules:

- **No invention.** Transcribe only what the DM described — do not invent names, effects, or entries that weren't specified. Record partial input as-is and leave gaps rather than filling them.
- **Match existing entries** in `beastiary.json` for field shapes and formatting.
- Include only fields that apply; omit any optional section with no content (`bonus_actions`, `reactions`, `villain_actions`, `conditions`, `traits`, `triggered_actions`, `part_breaks`, `variants`, `minor_variants`, `major_variants`, `equipment`, `manner`, `senses`, `languages`, `flavor`, `ecology`, `at_table`, `shadow`).
- `ability_scores`: always include all six (STR/DEX/CON/INT/WIS/CHA), each with `score` and `modifier` where `modifier = floor((score-10)/2)`.
- `roles` is always an array even with one element.
- `morale`: integer for a save threshold, string (e.g. "Does not flee") to bypass saves.
- Weapon-attack `on_hit` is always an array. Damage entries are `{ "damage": [{ "amount": "XdY+Z", "type": "type" }] }`; non-damage effects are plain strings in the same array. `on_miss` follows the same form.
- Passive abilities go in `traits` and use `effect` (not `description`). `triggered_actions` are non-reaction triggers (require `trigger` + `effect`); `reactions` respond to another creature's action.
- For mob entries, include `actions_per_round` and `max_attacks_per_opponent` on relevant actions, and use the mob-specific `variants`/`weapons` tables.

When any section matches an entry in `action_library.json`, prefer copying that entry (adjusting numbers) over writing a new one. When a variant/equipment/weapons table matches one in `component_library.tables`, copy that table verbatim. In both cases, tell the DM which library entry you used.

Show the full proposed JSON object (not wrapped in the outer `entries` array) and ask: **"Does this look right? Any changes before I append it?"**

Do not write anything to disk until the DM confirms.

## Phase 3: Append & Regenerate

After confirmation:

1. Read `GM_notes/Beastiary/data/beastiary.json`, insert the new entry as the last element of the `entries` array (before the closing `]}`), and write the file. Preserve existing formatting — 2-space indentation, blank lines between logical sections within entries, trailing newline.
2. Regenerate the derived artifacts (they resolve their own paths, so cwd doesn't matter, but run from `GM_notes/Beastiary/scripts/`):
   - `python3 json_to_library.py` → rebuilds `data/action_library.json`
   - `python3 json_to_md.py` → rebuilds `docs/Beastiary.md`

Confirm the entry was added, show the creature's name, and note that the library and markdown were regenerated.
