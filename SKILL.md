---
name: last30days
description: "Research a topic from the last 30 days on Reddit + X + Web, become an expert, and write copy-paste-ready prompts for the user's target tool."
allowed-tools: Bash, Read, Write, AskUserQuestion, WebSearch
---

# last30days: Research Any Topic from the Last 30 Days

Research ANY topic across Reddit, X, and the web. Surface what people are actually discussing, recommending, and debating right now.

## Parse User Intent

Before doing anything, extract:

1. **TOPIC**: What they want to learn about
2. **TARGET TOOL** (if specified): Where they'll use the prompts (e.g., "ChatGPT", "Midjourney")
3. **QUERY TYPE**:
   - **PROMPTING** - "X prompts", "prompting for X" → techniques + copy-paste prompts
   - **RECOMMENDATIONS** - "best X", "top X" → a LIST of specific things
   - **NEWS** - "what's happening with X" → current events
   - **GENERAL** - anything else → broad understanding

**Do NOT ask about target tool before research.** Run research first, ask after.

---

## Run the Research Script

```bash
python3 ~/.claude/skills/last30days/scripts/last30days.py "$ARGUMENTS" --emit=compact 2>&1
```

The script auto-detects available sources:
- **Firecrawl** → Reddit threads with real upvotes + general web pages
- **Bird** → X/Twitter posts with real likes (free, uses browser cookies)
- **OpenAI** → Reddit fallback if no Firecrawl
- **Zero config** → script still finds what it can; you supplement with WebSearch

**Depth flags** (pass through from user):
- `--quick` → Fewer sources, faster
- `--deep` → Comprehensive scan
- (default) → Balanced

---

## After the Script: Supplement with WebSearch

If the script output says `### WEBSEARCH SUPPLEMENT ###`, or if results are sparse, do WebSearch.

Choose queries based on QUERY_TYPE:
- **RECOMMENDATIONS**: `best {TOPIC} recommendations`, `{TOPIC} list examples`
- **NEWS**: `{TOPIC} news 2026`, `{TOPIC} announcement update`
- **PROMPTING**: `{TOPIC} prompts examples 2026`, `{TOPIC} techniques tips`
- **GENERAL**: `{TOPIC} 2026`, `{TOPIC} discussion`

**USE THE USER'S EXACT TERMINOLOGY** — don't substitute tech names based on your knowledge.
EXCLUDE reddit.com, x.com, twitter.com (covered by script).

---

## Synthesize and Present

**Ground your synthesis in the ACTUAL research content, not your pre-existing knowledge.**

### If RECOMMENDATIONS — extract specific names:
```
Most mentioned:
1. [Specific name] - mentioned {n}x (sources)
2. [Specific name] - mentioned {n}x (sources)
...
Notable mentions: [other things with 1-2 mentions]
```

### If PROMPTING/NEWS/GENERAL — show patterns:
```
What I learned:

[2-4 sentences synthesizing key insights FROM THE RESEARCH.]

KEY PATTERNS I'll use:
1. [Pattern from research]
2. [Pattern from research]
3. [Pattern from research]
```

**Then show stats:**
```
---
All sources reported back!
├─ Reddit: {n} threads │ {sum} upvotes │ {sum} comments
├─ X: {n} posts │ {sum} likes │ {sum} reposts
├─ Web: {n} pages │ {domains}
└─ Top voices: r/{sub1}, r/{sub2} │ @{handle1} │ {site}
```

**Then invite:**
```
---
Share your vision for what you want to create and I'll write a thoughtful prompt you can copy-paste directly into {TARGET_TOOL}.
```

**If TARGET_TOOL is unknown**, ask now (not before research).

**STOP and wait for the user to respond.**

---

## When User Shares Their Vision: Write ONE Prompt

**Match the FORMAT the research recommends:**
- Research says "JSON prompts" → write JSON
- Research says "structured parameters" → use key: value
- Research says "natural language" → use prose

### Output:
```
Here's your prompt for {TARGET_TOOL}:

---
[The prompt in the format the research recommends]
---

This uses [1-line explanation of which research insight you applied].
```

After delivering, offer: `Want another prompt? Just tell me what you're creating next.`

---

## Context Memory

For the rest of this conversation, you are an EXPERT on this topic.
- **DO NOT run new WebSearches** for follow-ups — answer from research
- **If they ask for a prompt** — write one using your expertise
- Only do new research if the user asks about a DIFFERENT topic
