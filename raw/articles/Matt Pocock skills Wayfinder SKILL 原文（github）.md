---
title: "Matt Pocock skills Wayfinder SKILL 原文（github）"
source: "https://github.com/mattpocock/skills/blob/main/skills/engineering/wayfinder/SKILL.md"
created: 2026-07-27
description: "mattpocock/skills 仓库 engineering/wayfinder/SKILL.md 一手英文原文（v1.1）。defuddle 不支持 text/plain（raw.githubusercontent.com），改用 web reader 提取 github blob 正文，内容为 SKILL.md 原文未改动。"
tags: [mattpocock-skills, Wayfinder, v1.1]
---

> [!info] 数据说明
> 源 URL：https://github.com/mattpocock/skills/blob/main/skills/engineering/wayfinder/SKILL.md
> 抓取方式：raw.githubusercontent.com 返回 text/plain，defuddle 仅处理 HTML 故失败；改用 web reader 提取 github blob 页正文 markdown。下方为 SKILL.md 一手英文原文。

---

|  |  |
| --- | --- |
| name | wayfinder |
| description | Plan a huge chunk of work — more than one agent session can hold — as a shared map of decision tickets on your issue tracker, and resolve them one at a time until the way to the destination is clear. |
| disable-model-invocation | true |

A loose idea has arrived — too big for one agent session, and wrapped in fog: the way from here to the __destination__ isn't visible yet. Wayfinding is about finding that way, not charging at the destination. This skill charts the way as a __shared map__ on the repo's issue tracker, then works its __decision tickets__ — questions whose resolution is a decision, not slices of a build to execute — one at a time until the route is clear.

The destination varies per effort, and naming it is the first act of charting — it shapes every ticket. It might be a spec to hand off and iterate on, a decision to lock before planning starts, or a change made in place like a data-structure migration. The map is domain-agnostic — engineering work, course content, whatever fits the shape.

## Plan, don't do

Wayfinder is __planning__ by default: each ticket resolves a decision, and the map is done when the way is clear — nothing left to decide before someone goes and does the thing. The pull to just do the work is usually the signal you've reached the edge of the map and it's time to hand off. An effort can override this in its __Notes__ — carrying execution into the map itself — but absent that, produce decisions, not deliverables.

## Refer by name

Every map and ticket is an issue, so it has a __name__ — its title. In everything the human reads — narration, the map's Decisions-so-far — refer to it by that name, never by a bare id, number, or slug. A wall of `#42, #43, #44` is illegible; names read at a glance. The id and URL don't vanish — a name wraps its link — but they ride _inside_ the name, never stand in for it.

## The Map

The map is a single issue on this repo's issue tracker, labelled `wayfinder:map` — the canonical artifact. Its tickets are child issues of the map.

The map is an __index__, not a store. It lists the decisions made and points at the tickets that hold their detail; a decision lives in exactly one place — its ticket — so the map never restates it, only gists it and links.

__Where the map, its child tickets, blocking, and frontier queries physically live is tracker-specific.__ The issue tracker should have been provided to you — run `/setup-matt-pocock-skills` if not. Consult the tracker doc's "Wayfinding operations" section for how _this_ repo expresses them. If no tracker has been provided, default to the local-markdown tracker.

### The map body

The whole map at low resolution, loaded once per session. Open tickets are __not__ listed — they are open child issues, found by query.

```
## Destination

<what reaching the end of this map looks like — the spec, decision, or change this effort is finding its way to. One or two lines; every session orients to it before choosing a ticket.>

## Notes

<domain; skills every session should consult; standing preferences for this effort>

## Decisions so far

<!-- the index — one line per closed ticket: enough to judge relevance, then zoom the link for the detail the ticket holds -->

- [<closed ticket title>](link) — <one-line gist of the answer>

## Not yet specified

<!-- see "Fog of war": in-scope fog you can't ticket yet; graduates as the frontier advances -->

## Out of scope

<!-- see "Out of scope": work ruled beyond the destination; closed, never graduates -->
```

### Tickets

Each ticket is a __child issue__ of the map; the tracker's issue id is its identity. Its body is the question, sized to one 100K token agent session:

```
## Question

<the decision or investigation this ticket resolves>
```

Each ticket carries a `wayfinder:<type>` label — one of `research`, `prototype`, `grilling`, `task` (see Ticket Types).

A session __claims__ a ticket by assigning it to the dev driving the map, __first__, before any work, so concurrent sessions skip it. That assignee _is_ the claim: an open, unassigned ticket is unclaimed.

Blocking uses the tracker's __native__ dependency relationship — essential because it renders the frontier _visually_ in the tracker's own UI, so the human sees what's takeable without opening the map. Only a tracker that lacks native blocking falls back to a body convention. A ticket is __unblocked__ when every ticket blocking it is closed; the __frontier__ is the open, unblocked, unclaimed children — the edge of the known.

The answer isn't part of the body — it's recorded on resolution (see Work through the map). Assets created while resolving a ticket are linked from the issue, not pasted in.

## Ticket Types

Every ticket is either __HITL__ — human in the loop, worked _with_ a human who speaks for themselves — or __AFK__, driven by the agent alone. A HITL ticket only resolves through that live exchange; the agent never stands in for the human's side of it (a grilling agent that answers its own questions has broken this).

- __Research__ (AFK): Reading documentation, third-party APIs, or local resources like knowledge bases to surface a fact a decision waits on. Resolved by a `/research` __subagent__. Use when knowledge outside the current working directory is required.
- __Prototype__ (HITL): Raise the fidelity of the discussion by making a cheap, rough, concrete artifact to react to — an outline, a rough take, a stub, or UI/logic code via the /prototype skill. Links the prototype as an asset. Use when "how should it look" or "how should it behave" is the key question.
- __Grilling__ (HITL): Conversation via the /grilling and /domain-modeling skills, one question at a time. The default case.
- __Task__ (HITL or AFK): Manual work that must happen before a _decision_ can be made — nothing to decide, prototype, or research, but the discussion is blocked until it's done. Signing up for a service so its API can be judged, provisioning access, moving data so its shape can be seen. This is the one type that _does_ rather than decides — and it earns its place by unblocking a decision, not by delivering the destination. The agent drives it alone where it can (AFK); otherwise it hands the human a precise checklist (HITL). Resolved when the work is done; the answer records what was done and any resulting facts (credentials location, new URLs, row counts) later tickets depend on.

## Fog of war

The map is _deliberately_ incomplete: don't chart what you can't yet see. Beyond the live tickets lies the __fog of war__ — the dim view of decisions and investigations you can tell are coming but can't yet pin down, because they hang on questions still open. Resolving a ticket clears the fog ahead of it, graduating whatever's now specifiable into fresh tickets — one at a time, until the way to the destination is clear and no tickets remain.

The map's __Not yet specified__ section is where that dim view is written down: the suspected question, the area to revisit later. It's the undiscovered frontier _toward_ the destination — everything here is in scope, just not sharp enough to ticket. Write as loosely or as fully as the view allows; it doubles as a signpost for collaborators reading where the effort is headed.

__Fog or ticket?__ The test is whether you can state the question precisely now — _not_ whether you can answer it now.

- __Ticket when__ the question is already sharp — even if it's blocked and you can't act on it yet.
- __Not yet specified when__ you can't yet phrase it that sharply. Don't pre-slice the fog into ticket-sized pieces: it's coarser than a ticket, and one patch may graduate into several tickets, or none, once the frontier reaches it.

__Not yet specified__ excludes what's already decided (Decisions so far), what's already a live ticket, and what's out of scope (the next section).

## Out of scope

Fog only ever gathers _toward_ the destination. The destination fixes the scope, so work beyond it is __out of scope__ — it isn't fog, and it doesn't belong in __Not yet specified__. It gets its own __Out of scope__ section on the map: work you've consciously ruled out of _this_ effort. Scope, not sharpness, lands it here.

Out-of-scope work never graduates — the frontier stops at the destination — so it returns only if the destination is redrawn, and then as a fresh effort, not a resumption.

Ruling something out of scope is a scoping act, not a step on the route. When a ticket that already exists turns out to sit past the destination — mis-scoped in while charting, or exposed by a resolution — __close it__ (a closed ticket is unambiguously off the frontier) and leave one line in the __Out of scope__ section: the gist plus why it's out of scope, linking the closed ticket. It stays out of __Decisions so far__, which records the route actually walked — a scope boundary isn't a step on it.

## Invocation

Two modes. Either way, __never resolve more than one ticket per session__ — with the exception of research tickets.

### Chart the map

User invokes with a loose idea.

1. __Name the destination.__ Run a `/grilling` and `/domain-modeling` session to pin down what this map is finding its way to — the spec, decision, or change. The destination fixes the scope, so it's settled first.
2. __Map the frontier.__ Grill again, __breadth-first__ this time: fan out across the whole space rather than deep on any one thread, surfacing the open decisions and the first steps takeable now. __If this surfaces no fog__ — the way to the destination is already clear, the whole journey small enough for one session — you don't need a map. Stop and ask the user how they'd like to proceed.
3. __Create the map__ (label `wayfinder:map`): Destination and Notes filled in, Decisions-so-far empty, the fog sketched into __Not yet specified__.
4. __Create the tickets you can specify now__ as child issues of the map — then wire blocking edges in a __second pass__ (issues need ids before they can reference each other). Wiring sorts them into the frontier and the blocked; everything you can't yet specify stays in the fog — the __Not yet specified__ section.
5. __Fire the research subagents.__ For each `research` ticket you just created, spin up a `/research` subagent to resolve it in parallel, capturing its findings on a throwaway `research/<name>` branch with a context pointer from the ticket.
6. Stop — charting is one session's work; it hand-resolves nothing.

### Work through the map

User invokes with a map (URL or number). A ticket is __optional__ — without one, you pick the next decision, not the user.

1. Load the __map__ — the low-res view, not every ticket body.
2. Choose the ticket. If the user named one, use it. Otherwise take the first frontier ticket in order. __Claim it__: assign it to yourself before any work.
3. Resolve it — __zoom as needed__: fetch the full body of any related or closed ticket on demand; invoke the skills the `## Notes` block names. If in doubt, use `/grilling` and `/domain-modeling`.
4. Record the resolution: post the answer as a __resolution comment__, __close__ the issue, and __append a context pointer__ to the map's Decisions-so-far.
5. Add newly-surfaced tickets (create-then-wire); graduate any fog the answer has made specifiable, clearing each graduated patch from __Not yet specified__ so it lives only as its new ticket. If the answer reveals a ticket — this one or another — sits beyond the destination, __rule it out of scope__ rather than resolving it on the route. If the decision invalidates other parts of the map, update or delete those tickets.

The user may run unblocked tickets in parallel, so expect other sessions to be editing the tracker concurrently.
