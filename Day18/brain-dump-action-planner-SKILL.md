---
name: brain-dump-action-planner
description: Transform messy notes, meeting transcripts, voice memo dumps, brainstorming sessions, and stream-of-consciousness thoughts into a structured, interactive HTML dashboard with a summary, key takeaways, action items, open questions, risks, and conflicts. Use this whenever the user pastes in raw notes, a meeting transcript, a voice memo transcription, or asks to "organize this," "make sense of this," "turn this into action items," "summarize this meeting," or "merge these notes" — even if they don't use the words "brain dump" or "action plan." Also use when the user provides multiple related notes/transcripts and wants them combined into one coherent plan. Do not use for polished documents that are already well-organized, or for generating new content from scratch (this skill only organizes what's already provided).
---

# Brain Dump Action Planner

Turn unstructured input — messy notes, meeting transcripts, voice memo transcriptions, brainstorms — into a clean, interactive HTML dashboard. The core job is organization, not authorship: every fact in the output must trace back to something the user actually wrote or said. The value of this skill comes from trustworthy extraction, not creative synthesis, so treat "don't invent anything" as the most important constraint, more important than making sections feel complete.

## Choosing a mode

Look at the input and pick one mode before you start:

- **Transcript Mode** — the input has speaker labels (names, "Speaker 1:", initials followed by colons, etc.) and reads like a conversation or meeting.
- **Merge Mode** — the user provides two or more separate notes/transcripts/sources and wants them combined into a single plan.
- **Full Breakdown** — everything else: a single unstructured note, brainstorm, or voice memo transcript with no distinct speakers.

If it's ambiguous whether something is Merge Mode (e.g., one long note that references an earlier note), ask the user rather than guessing — merging behavior (duplicate detection, conflict surfacing) only makes sense once you know the input actually comes from separate sources.

## The one rule that matters most

Never add, infer, assume, predict, estimate, or complete missing information. If a deadline, owner, or detail isn't in the source material, the field says exactly `Not specified` — don't leave it blank, don't guess based on context, and don't write something plausible-sounding to fill the space. This applies to every field in every section. It's fine, expected even, for a brain dump to have lots of "Not specified" fields — that's useful signal to the user about what's actually missing, and papering over it defeats the point of the tool.

Preserve names, dates, numbers, and terminology exactly as given — don't normalize "Q3" to "third quarter," don't expand initials, don't correct what might look like a typo in a name. If the source is inconsistent (calls someone "Raj" in one place and "Rajesh" in another), preserve that inconsistency rather than picking one — that's exactly the kind of thing that belongs in the Conflicts section.

## Output requirement

Produce a single self-contained HTML artifact and output nothing else — no preamble, no markdown, no explanation before or after. The artifact should start directly with a `<style>` block.

Build it as a modern, mobile-responsive dashboard — think Notion, Linear, Asana, ClickUp, Airtable. Use cards, section headers, tables, hover effects, soft shadows, and generous whitespace so it reads as a polished internal tool rather than a wall of text. Action items are the most important section — make them visually prominent (larger card, top placement, or a distinct accent color), since that's usually what the user is going to act on first. For any note long enough that a section would require a lot of scrolling, make that section collapsible so the dashboard stays scannable at a glance.

Everything shown must come directly from the source. If a section has nothing to put in it (no risks were mentioned, no open questions came up), still render the section but show a light "None noted" state rather than omitting the section entirely — that consistency helps the user trust that you looked, rather than wondering if you missed something.

### Status badges

Use these consistently wherever priority/status applies:

| Badge | Meaning |
|---|---|
| 🔴 | High Priority |
| 🟠 | Medium Priority |
| 🟢 | Low Priority |
| ⚠️ | Conflict |
| ❓ | Open Question |
| ✅ | Completed |
| ⏳ | Pending |

Only assign a priority or status badge when the source material actually signals it (e.g., the note says "urgent" or "ASAP" → 🔴; a task described as "whenever, no rush" → 🟢). If the source gives no signal either way, don't force a priority badge onto it — leave priority as `Not specified` rather than defaulting to Medium.

## Required sections (all modes)

Render these in order:

1. **Summary** — A short, neutral overview of what the note/meeting/dump is about. A few sentences, not a restatement of every line.
2. **Key Takeaways** — The most important points, as cards or a highlight grid. Pull out what the source itself emphasizes, not what you think is important.
3. **Action Items** — An interactive table (sortable/filterable if practical) with columns: Task, Owner, Deadline, Status. Every row's Owner/Deadline/Status is `Not specified` unless the source states it.
4. **Open Questions** — Unresolved topics, undecided items, things explicitly flagged as "TBD" or "need to figure out," or questions raised but not answered in the source.
5. **Risks / Blockers** — Anything the source frames as a dependency, blocker, risk, or concern.
6. **Conflicts** — Contradictions in the source: two different deadlines given for the same thing, two people both assigned as owner, a decision stated one way early on and differently later. Use the ⚠️ badge here. This section is about surfacing tension in the source material, not resolving it.
7. **Additional Notes** — Context or details that don't fit cleanly into the sections above but are still worth keeping.

## Transcript Mode additions

Add these sections, and use the exact speaker labels as they appear in the transcript (don't rename "J:" to "James" unless the transcript itself does that mapping):

- **Speaker Summary** — What each speaker's main contributions/positions were.
- **Decisions by Speaker** — Group any decisions by who made or proposed them.
- **Action Items by Speaker** — Same Action Items table, but grouped or filterable by owner.
- **Attribution Notes** — Whenever an action item or decision's ownership is ambiguous (e.g., "someone should look into this" with no clear speaker taking it), call that out explicitly here rather than picking an owner. This is a common failure mode to watch for — don't let an ambiguous "we'll handle it" turn into a specific person's action item.

## Merge Mode additions

Add these sections when combining multiple sources:

- **Source Information** — List the sources being merged (however the user identified them — file names, "note 1 / note 2", dates, etc.).
- **Duplicate Items Section** — Action items, decisions, or takeaways that appear in more than one source. Show them together so the user can see the overlap.
- **Conflict Resolution Review Section** — Every place the sources disagree (different deadlines, different owners, contradictory decisions). List both/all versions side by side with their source. Never pick a winner or silently merge conflicting values into one — that decision belongs to the user, and Merge Mode's whole value is surfacing these tensions rather than hiding them.
- **Source Note** — A brief note on how the merge was done (e.g., "combined 3 notes; overlapping items shown in Duplicates; conflicting details shown in Conflict Resolution Review").

## Working through the input

1. Read the entire input first before generating anything — skimming and generating simultaneously is how details get missed or misattributed.
2. Extract facts section by section rather than trying to hold the whole categorization in your head at once: pull out every task-like statement, every date, every name, every question mark first, then sort them into the required sections.
3. When something could belong in more than one section (e.g., a risk that's also phrased as an open question), pick the section that matches how the source frames it, and it's fine for related items to be cross-referenced or mentioned briefly in both if that helps the user.
4. Before finalizing, scan back through the source once more specifically looking for anything you organized that isn't actually traceable to a specific line — that's the check that catches invented content.
