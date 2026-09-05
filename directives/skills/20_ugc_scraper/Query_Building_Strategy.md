# Query Building Strategy v2

6 queries covering 6 canonical coverage slots. Each query is 2 to 4 words.
Keep them short. Long queries dilute TikTok's native search signal and return fewer results per page.

## The 6 slots

### 1. Pain in customer language
The raw, unfiltered phrase a sufferer would type. Pull from the VOC "Problem-space language" section.

**Good:** `ads stopped working`, `skin purging`, `cant scale meta`, `my product review`
**Bad:** `Meta advertising underperformance issues` (too formal, no one searches like this)

### 2. Solution or AI workflow
How customers describe the fix. Should be aspirational but specific.

**Good:** `ai ads that actually work`, `retinol routine`, `meta creative system`
**Bad:** `advertising software` (generic, returns SaaS ads not UGC)

### 3. Identity / ICP + category
Videos by the target audience about the category. Pull the ICP label from the VOC.

**Good:** `dtc founder meta ads`, `media buyer strategy`, `paid social agency`
**Bad:** `small business owner` (too broad, returns any entrepreneur content)

### 4. Problem-aware raw emotion
A blunt statement of the problem. Short, emotional.

**Good (esempio nicchia ads/marketing):** `meta ads not spending`, `facebook ads broken`, `roas tanking`
**Bad (tested 2026-04-22):** `facebook ads dying` ,  pulls Facebook account-help/verification noise

**Avoid queries containing bare platform names** (`facebook`, `meta`) without modifiers. Platform names alone surface account-support content.

### 5. Workflow / how-to
Instructional intent. Surfaces content creators teaching the workflow.

**Good:** `ai ugc ads tutorial`, `creative strategy workflow`, `retinol how to`
**Bad:** `tutorial` (way too broad)

### 6. Peer trust / format pattern
Format-specific TikToks inside the niche. DIML, POV, reaction, etc.

**Good:** `media buyer day in the life`, `diml social media manager`, `pov running ads`
**Bad:** `day in the life` (returns office, nurse, teacher, barista vlogs)

## Rules

1. **Minimum 2 words** per query. Single-word queries return too-broad results.
2. **Maximum 4 words** per query. Longer queries hit TikTok's "zero results" wall.
3. **No quotes, no operators.** TikTok search does not support boolean operators or exact-match quotes.
4. **Don't reuse the same noun** across two queries. E.g. don't have both `meta ads fatigue` and `meta ads broken` ,  overlap ~60%.
5. **One query must include the product category in the customer's own words.** This becomes the lowest-relevance-but-highest-volume net.

## Validation step

After extracting the 6 queries, sanity check each:

- Can you picture a real creator making a video matching this query? If not, rewrite.
- Does it contain only the audience's language, or terms from the brand's marketing copy? Strip out marketing language.
- Does it include any platform name without modifier (bare `facebook`, `tiktok`, `meta`)? If so, add a modifier or rewrite.

## Example set: VOC nicchia ads/marketing (validated 2026-04-22)

These 6 queries produced avg relevance 8.9/10 in a real run:

1. `meta ads creative fatigue` (pain)
2. `ai ads that actually work` (solution)
3. `dtc founder meta ads` (identity + category)
4. `meta ads not spending` (problem-aware, replaced the broken `facebook ads dying`)
5. `ai ugc ads tutorial` (workflow)
6. `media buyer day in the life` (peer trust)

## When to iterate

If the relevance vet in Step 5 of SKILL.md passes fewer than 20 candidates of the 40 candidates at score ≥7, rewrite the weakest-performing queries rather than lowering the threshold. The fix is upstream, not downstream.
