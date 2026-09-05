# Plain Text Output Format v2

Single `.txt` file. Designed to be both human-readable and machine-parseable by the downstream `/ugc` skill.

The `build_swipe.py` script generates this file. This doc is the spec it implements.

## Full Template

```
================================================================================
UGC SCRAPER 2.0, VIRAL TIKTOK WINNERS, {{NICHE_TITLE_UPPER}}
================================================================================

Niche:             {{NICHE_TITLE}}
Date:              {{YYYY-MM-DD}}
Source:            tiktok_organic
Pipeline:          scraptik (scrape) + LLM relevance vet + scrape-creators (transcripts)

Final winners:     {{FINAL_COUNT}}
Transcripts:       {{WHISPER_N}} usable / {{UNAVAIL_N}} unavailable
Pure breakouts:    {{BREAKOUT_COUNT}}
Avg relevance:     {{AVG_REL}}/10
Avg scoring:       {{AVG_SCORE}}

================================================================================
  WINNER #1    |    REL: {{REL}}/10    |    SCORE: {{SCORE}}    |    {{BREAKOUT_TAG_IF_APPLICABLE}}
================================================================================

CREATOR:       @{{USERNAME}}{{VERIFIED_TAG}}
FOLLOWERS:     {{FOLLOWERS_FORMATTED}}
RATIO:         {{RATIO}}x views per follower

METRICS:       {{VIEWS}} views, {{LIKES}} likes, {{SHARES}} shares, {{COMMENTS}} comments, {{SAVES}} saves
DURATION:      {{DURATION}} seconds
AGE:           {{AGE_DAYS}} days ago ({{UPLOADED_AT_FORMATTED}})

HASHTAGS:      {{HASHTAGS_COMMA_SEPARATED}}
AUDIO:         {{SONG_TITLE}}
SEARCH QUERY:  {{SEARCH_QUERY}}

TIKTOK URL:    {{TIKTOK_URL}}
CAPTION TEXT:  {{CAPTION_UP_TO_500_CHARS}}
TRANSCRIPT:    {{TRANSCRIPT_SOURCE_LABEL}}

HOOK LINE:
>>> {{HOOK_LINE}}

FULL TRANSCRIPT:
{{FULL_TRANSCRIPT}}

================================================================================
  WINNER #2    |    REL: {{REL}}/10    |    SCORE: {{SCORE}}    |    {{BREAKOUT_TAG_IF_APPLICABLE}}
================================================================================

... (same structure for all 25 winners)

================================================================================
END OF SWIPE FILE
================================================================================

HOW TO USE THIS FILE:

1. Open the /ugc skill
2. Attach or paste this file when the skill asks for UGC inspiration
3. The script writer learns from these real viral hooks and writes new scripts
   grounded in actual language that stopped scroll in your niche

v2 guarantee: every winner here passed an LLM relevance vet against your VOC.
No off-niche noise, no unrelated viral content padding the file.

Scoring notes:
  REL (0-10) = VOC relevance. Higher is more directly on-niche.
  SCORE combines views, creator underdog ratio, engagement quality, recency.
  BREAKOUT WINNER = follower floor 100+ AND views/followers >= 50.
  These are cleanest examples of hooks carrying videos (not audience size).
```

## What's new vs v1

- **REL field** on every winner block. v1 had no relevance scoring.
- **CAPTION TEXT field** added below the TikTok URL. Preserves text-overlay hooks which speech transcripts miss.
- **SEARCH QUERY field** shows which of the 6 queries surfaced this video. Useful for query tuning.
- **Sort order** is relevance DESC, then final_score DESC (v1 was final_score only).
- **Header** includes pipeline description so downstream tools know v2 was used.

## Formatting rules

### Number formatting
- Under 1,000: exact (`847`)
- 1,000 to 999,999: one decimal + K (`12.4K`, `450.0K`)
- 1,000,000+: one decimal + M (`2.3M`, `12.1M`)

Apply to: views, likes, shares, comments, saves, followers.

### Ratio
`{{RATIO}}` = views / followers.
- Under 100: one decimal (`2.4`, `85.1`)
- 100 or more: integer (`150`, `4416`)

### Verified tag
If `channel.verified` is true, append ` (verified)` to username. Otherwise empty.

### Breakout tag
If `_scoring.underdog_flag` is true: `BREAKOUT WINNER` in the fourth pipe slot.
Otherwise: skip the fourth slot entirely (no empty pipe).

Breakout requires BOTH: `views/followers >= 50` AND `followers >= 100`.

### Transcript source label
- `whisper_fallback`: display as `WHISPER AI (FALLBACK)`
- `unavailable`: display as `UNAVWORKDIRLE`

### Transcript unavailable
Replace HOOK LINE and FULL TRANSCRIPT with:

```
HOOK LINE:
>>> (transcript unavailable, rely on caption above)

FULL TRANSCRIPT:
This video does not have usable speech content. Caption and hashtags are
above for reference. Text-overlay hooks may be present in the video itself.
```

The v2 swipe file relies on the CAPTION TEXT field to provide hook context when transcript is unavailable.

### Hashtags
Top 5, prefixed with `#`, joined by `, `. If fewer than 5 exist, join all. If zero, render `none`.

### Song title
- `music.title` contains "original sound" + artist → `original sound by @{{artist}}`
- Has artist → `{{title}} by {{artist}}`
- No artist → `{{title}}`
- No title → `no audio info`

Trim to 60 chars max.

### Age and date
- `{{AGE_DAYS}}` = integer days between `uploadedAt` unix and now.
  - 0 → `today`
  - 1 → `1 day ago`
  - 2+ → `{{N}} days ago`
- `{{UPLOADED_AT_FORMATTED}}` = human readable, e.g. `April 8, 2026`

### Line widths
Every separator (`===` or `---`) is exactly 80 characters.

Transcript body text is NOT wrapped ,  let it flow naturally. Downstream skill parses it, text wrapping would break sentence reconstruction.

### HTML escape
None. Plain text. Pass through all characters. Only strip control characters (`\x00` through `\x1F` except `\n` and `\t`).

### No em dashes or hyphens as separators
User preference: never use em dashes or hyphens as sentence separators. Use commas, periods, or line breaks.

**Allowed:** compound words like `pay-per-use`, `well-known`, `on-niche`.
**Banned:** `Real hooks ,  real transcripts ,  ready to use.` (em dash)
**Banned:** `Real hooks, real transcripts - ready to use.` (hyphen as sentence separator)

## Ordering

Winners render in: `relevance DESC, final_score DESC`. Highest REL at the top. Ties broken by scoring.

`build_swipe.py` sorts in memory before writing.

## File location

The build script accepts the per-project root via `--output-dir` and writes under that root only. It never touches `~/Desktop`. On Windows with OneDrive Known Folder Move enabled, the swipe file always lands in the project folder you opened Claude Code in, never in a OneDrive-synced home redirect.

```
$WORKDIR/05_UGC/scraper/<niche-slug>/ugc-winners-v2-<YYYY-MM-DD>.txt
```

If the subdirectory cannot be created, fall back to:

```
$WORKDIR/05_UGC/scraper/ugc-winners-v2-<YYYY-MM-DD>-<niche-slug>.txt
```

## Typical file size

40 KB to 80 KB depending on transcript length. Well under any context limit.
