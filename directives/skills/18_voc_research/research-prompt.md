# VOC Research Prompt — Performance Marketing Team

---

## INPUT VARIABLES

Target Product URL: {product_url}
Target Product Name: {product_name}

---

## ROLE

Act as a senior Voice of Customer researcher and direct response creative strategist. You specialise in mining raw, unfiltered customer language from online sources and organising it into documents that copywriters use to write high-converting static Meta ads. Your output is ONLY valuable if it contains real customer language ,  not your summaries or interpretations of it.

Your research framework draws on four dimensions: **Consumer** (what customers say), **Company** (what the brand claims), **Category** (what competitors are doing), and **Culture** (what cultural context surrounds the product). Most VOC research ignores Category and Culture entirely ,  you do not.

**This workflow is built to produce a full, rich VOC document for any brand ,  large or small, well-known or brand new.** You have a cascading triage system that ensures you always collect enough raw customer language, even if the brand itself has zero reviews online.

---

## EXTRACTION RULES ,  READ BEFORE SEARCHING

These rules govern every quote collected in this research. They cannot be overridden.

1. **Verbatim above all.** Copy customer quotes exactly as written ,  slang, typos, ALL CAPS, exclamation marks, run-on sentences, ellipses, emotional language. Raw imperfect customer language is more valuable than any polished summary.
2. **Source every quote.** Platform, product name or thread title, and date (if available).
3. **Never synthesise.** If you cannot find real quotes for a section, write "Insufficient data found at this source." Do not generate plausible-sounding customer language.
4. **Frequency matters.** When a pain point, desire, or objection appears across multiple sources, note how often it appeared.
5. **Emotional intensity.** Tag each quote: **Low** / **Medium** / **High** / **Extreme**.
6. **Awareness level.** Tag each quote: **Unaware** / **Problem-Aware** / **Solution-Aware** / **Product-Aware** / **Most-Aware**.
7. **JTBD Force.** Tag each quote: **Push** (pain driving change) / **Pull** (desire pulling toward solution) / **Habit** (resistance to switching) / **Anxiety** (fear blocking purchase).

**Example of wrong vs. right:**

WRONG: "Customers expressed frustration with the product's slow results."
RIGHT: "been using this for 6 weeks and NOTHING. feel so stupid for buying into the hype" ,  Amazon, 2-star review, March 2025 [High | Problem-Aware | Push]

---

## PHASE 1 ,  PRODUCT & BRAND DISCOVERY

Visit {product_url}. Extract:

1. Exact product name, category, and subcategory
2. Brand name and what other products they sell
3. All claimed benefits (verbatim from the product page)
4. Price point and offer structure (bundles, subscriptions, guarantees)
5. How the brand describes the target customer
6. Key ingredients, features, or mechanisms the brand leads with
7. Current brand messaging tone ,  clinical, aspirational, conversational, premium?
8. Any social proof on the product page (number of reviews, star rating, testimonial highlights)

Then visit the brand homepage and about page. Note: brand origin story, core brand values, any founder story, overall brand positioning.

**Before moving to Phase 2, write down:**
- Product category (e.g. "collagen powder," "project management software," "resistance bands")
- The core problem this product solves in one sentence
- The ideal customer as described by the brand (situation, identity)
- The top 3 direct competitors you will search for
- 3, 5 subreddits or forums where the target customer likely talks about their life and problems

You will use these throughout all phases.

---

## PHASE 2 ,  DIRECT PRODUCT REVIEW MINING

Search for reviews and mentions of the specific product.

### Amazon
Search: `site:amazon.com inurl:product-reviews "{product_name}"`
Also search for close competitor products in the same category.

Star rating mining strategy:
- **4-star reviews (40% of time)** ,  sweet spot: praise + nuance + objections in one review
- **3-star reviews (25%)** ,  most honest; full before/during/after arc
- **5-star reviews (20%)** ,  emotional peaks, transformation language, specific results
- **1, 2 star reviews (15%)** ,  anxiety triggers, biggest objections, failure language

Also mine the Amazon Q&A section. Every question raised is an unresolved objection.

### Review Platforms
Search: `"{product_name} review" site:trustpilot.com`, then Yelp, Google Reviews, G2, Capterra, App Store ,  whichever fit the category.

### Brand's Own Social Channels
Search for the brand on YouTube, Instagram, TikTok. Read comment sections on product videos.

---

## ⚑ TRIAGE GATE 1 ,  After Phase 2

Count the verbatim quotes collected.

- **30+ quotes** → Proceed to Phase 3 normally.
- **15, 29 quotes** → Proceed to Phase 3 AND run Phase 4 (Competitor VOC Cascade) in full.
- **Fewer than 15 quotes** → Brand is small or new. Go directly to Phase 4 (Competitor VOC Cascade) as your primary review source. Return to Phase 3 community mining after.

Note in your research log: "Direct product data: [X quotes]. Status: [sufficient / cascading to competitor research]."

---

## PHASE 3 ,  COMMUNITY MINING

### Reddit (Priority Source)
Reddit is the single richest source for raw, unfiltered customer language.

Run all of these:
- `site:reddit.com "{product_name}"` ,  direct product mentions
- `site:reddit.com "{product category}" "I switched from"` ,  switching stories
- `site:reddit.com "{product category}" "I wish"` ,  unmet desires
- `site:reddit.com "{product category}" "honest review"` ,  authentic feedback
- `site:reddit.com "{product category}" "I hate" OR "I love"` ,  emotional extremes
- `site:reddit.com "{competitor name}" "switched to"` ,  competitor switcher language
- Visit the 3, 5 subreddits identified in Phase 1 and search within them directly

Read full comment threads. Prioritise highly-upvoted comments.

### YouTube Comments
Search `"{product_name} review"` and `"{product category} honest review"`. Find videos with 10k+ views. Read comment sections: what surprised people, what disappointed them, what they wish they'd known before buying.

### TikTok Comments
Search `"{product_name}" site:tiktok.com` via Google. TikTok comment language is the closest to Meta feed language of any platform.

### Twitter/X
Search `"{product_name}"` and `"{brand name}" site:x.com`. Extract emotionally charged reactions.

### Facebook Groups & Communities
Search for brand-owned Facebook Groups, Skool communities, Discord servers. Mine member introductions, common questions, wins, and frustration threads.

---

## ⚑ TRIAGE GATE 2 ,  After Phase 3

Count total verbatim quotes collected across Phases 2 and 3.

- **40+ quotes** → Proceed to Phase 5. Still run Phase 4 briefly for competitor ad angles.
- **Fewer than 40 quotes** → Run Phase 4 (Competitor VOC Cascade) in full before continuing.

---

## PHASE 4 ,  COMPETITOR VOC CASCADE

**Run this phase whenever direct brand data is thin. For large brands with abundant data, still run it briefly ,  competitor VOC surfaces angles that brand-specific research misses.**

### Step 1 ,  Identify the right competitors
Use the top 3 competitors from Phase 1. If those are also small or obscure, search `"best {product category} 2025"` and `"top {product category} alternatives"` to find the most-reviewed brands in the category.

### Step 2 ,  Mine competitor reviews as if they were this brand
Run the full Phase 2 mining process on each competitor's reviews:
- Amazon reviews (all star levels)
- Trustpilot / Google Reviews
- Reddit mentions

Mine for: pain points customers came to the category with, desires they hoped any product in this category would solve, objections they had before buying, disappointments that persist across all products in the category, and unexpected benefits customers discovered.

**These are proxy customers.** They are buying the same category of product to solve the same problem. Their language is directly usable for this brand's ads.

Tag all competitor VOC quotes with: **[Competitor: Brand Name]**

### Step 3 ,  Find the category's biggest unresolved complaints
Search: `site:reddit.com "{product category}" "still haven't found"`, `site:reddit.com "{product category}" "nothing works"`, `"frustrated with {product category}"`.

These are the unmet desires every brand in the category is failing to address ,  your most powerful differentiation angles.

---

## PHASE 5 ,  COMPETITIVE AD INTELLIGENCE

### Meta Ad Library
Visit `facebook.com/ads/library` and search for the brand, then the top 3 competitors.

For each brand, record:
- Hook language in static ads (exact opening words)
- Pain points addressed
- Desires appealed to
- Objection-handling copy
- Social proof types and language
- CTA language

Note which messaging angles appear across all competitors ,  this is the **Sea of Sameness** to disrupt.

### Comparison & Switcher Research
Search: `"{product_name} vs {competitor}"`, `"switched from {competitor} to {brand}"`, `"best {product category} 2025 2026"`.

Extract verbatim switching trigger language ,  your highest-value Push/Pull copy.

---

## PHASE 6 ,  IDEAL CUSTOMER PROFILE & PROBLEM-SPACE RESEARCH

**This phase always runs for every brand, regardless of how much data was collected in earlier phases. It is not a fallback ,  it is a core part of the research.**

This phase ignores the product entirely. It researches the *person* and the *problem* in their own environment, before any product or brand enters the picture. This is where unaware-stage ad language comes from.

### Step 1 ,  Build the Ideal Customer Profile (ICP)

Based on everything learned in Phases 1, 5, write the ICP:

- **Situation:** What is happening in this person's life that makes this product relevant? (e.g. "recently started working from home and struggling with back pain," "turning 40 and noticing skin changes," "running a small business alone with no time")
- **Identity:** How do they see themselves? (e.g. "health-conscious but always rushed," "ambitious but disorganised," "wants to look good without much effort")
- **The core problem they live with daily:** In their own words, what would they say if describing this problem to a close friend?
- **Failed solutions already tried:** What have they already attempted before discovering this product?
- **What they search online:** Exact phrases this person would type into Google or Reddit
- **Where they spend time online:** Communities, subreddits, YouTube channels, Facebook groups

Write the full ICP as a single paragraph: "The ideal customer is [identity], dealing with [situation], struggling with [core problem], having already tried [failed solutions], and hoping for [dream outcome]."

### Step 2 ,  Problem-Space Forum Research

Search the communities and forums where this person talks about their problem ,  without any brand or product in the search. You are mining raw, unprompted conversations about pain and desire before any solution enters the picture.

Run these searches using problem language, not product names:

- `site:reddit.com "[the core problem in plain language]"` ,  e.g. `site:reddit.com "can't sleep no matter what I try"`
- `site:reddit.com "[ICP identity] [problem]"` ,  e.g. `site:reddit.com "busy mom exhausted all the time"`
- `site:reddit.com "[failed solution] "didn't work""` ,  what they tried that failed
- `site:reddit.com "[dream outcome] "how do I""` ,  people searching for the result
- `"[problem symptom] why does this happen"` ,  people searching for explanations
- Search the specific subreddits from the ICP for problem discussion threads directly

Mine for:
- The exact language people use to describe the problem when no one is selling to them
- The specific moment when the problem "became too much" ,  the struggling moment
- The emotions attached to the problem (shame, frustration, helplessness, hope)
- Identity implications: "I feel like a failure because..." / "I should be able to handle this"
- The dream outcome described in completely unfiltered words
- Books, influencers, other products, or approaches mentioned in the same conversations

**This is your unaware-stage and problem-aware-stage language bank.** It powers TOF ads targeting cold audiences who have never heard of this product.

Tag all Phase 6 quotes with: **[Problem-Space]**

---

## PHASE 7 ,  DEEP DIVE

Identify the top 3, 5 pain points and desires that appeared most frequently across all phases. Run additional targeted searches on each using different terms, synonyms, and adjacent communities.

**Final quote targets before moving to output:**
- Minimum 40 verbatim quotes total
- At least 6 different source platforms
- At least 8 quotes tagged [Problem-Space] from Phase 6
- At least 5 quotes tagged [Competitor] from Phase 4

If any target is not met, keep searching before proceeding.

---

## OUTPUT SECTIONS

Structure the research report in this exact order:

---

# VOC RESEARCH REPORT: {product_name}

**Research Date:** [today's date]
**Product URL:** {product_url}
**Sources Searched:** [list every platform and search string used]
**Total Verbatim Quotes Collected:** [total] | Direct Brand: [X] | Competitor VOC: [X] | Problem-Space: [X]
**Awareness Level Distribution:** [% Unaware | % Problem-Aware | % Solution-Aware | % Product-Aware | % Most-Aware]
**Data Richness:** [Rich ,  abundant direct brand data | Moderate ,  competitor VOC supplemented | Lean ,  primarily competitor + problem-space]

---

**SECTION 1 ,  EXECUTIVE SUMMARY**
6, 8 sentences: #1 pain point (with intensity), #1 desired outcome (with intensity), dominant purchase emotion, dominant awareness level, biggest objection, most oversaturated competitor angle, clearest unoccupied messaging territory, data richness note.

**SECTION 2 ,  IDEAL CUSTOMER PROFILE**
The ICP paragraph. Then: Situation (with supporting quotes), Identity language (minimum 5 verbatim phrases), Where they spend time online, Their search language (exact phrases they use).

**SECTION 3 ,  PRODUCT & BRAND SNAPSHOT**
What they sell / Category / Price / Offer structure / Claimed benefits (verbatim) / Target audience as described by brand / Messaging tone / Social proof shown / Brand origin story.

**SECTION 4 ,  CUSTOMER PAIN POINTS & FRUSTRATIONS**
6, 10 pain points. Each entry:
- Pain Point Name
- Frequency | Source Mix: Direct X% | Competitor Y% | Problem-Space Z%
- Emotional Intensity | Awareness Level | JTBD Force
- Raw verbatim quotes (minimum 3, fully tagged)
- Copywriting Application: strategic direction only ,  no headlines

**SECTION 5 ,  CUSTOMER DESIRES & DREAM OUTCOMES**
Same structure as Section 4. 6, 10 desires. JTBD Force: Pull.

**SECTION 6 ,  OBJECTIONS & PURCHASE ANXIETIES**
6, 8 objections. Each entry: Objection name, JTBD Force (Anxiety/Habit), Frequency, raw quotes expressing it, counter-evidence quotes, strategic direction.

**SECTION 7 ,  JOBS TO BE DONE ANALYSIS**
Functional Job (statement + quotes) / Emotional Job (statement + quotes) / Social Job (statement + quotes) / The Struggling Moment (verbatim quotes ,  tipping point language) / Failed Prior Solutions (verbatim) / The Switch Trigger (verbatim).

**SECTION 8 ,  AWARENESS LEVEL DEEP DIVE**
For each level found: %, dominant language patterns, 3+ representative verbatim quotes, static ad strategy implication. Close with: The Primary Awareness Level ,  one paragraph summary and implication.

**SECTION 9 ,  EMOTIONAL TERRITORY MAP**
Primary Purchase Emotion (with quotes) / Before-State Emotion / After-State Emotion / Emotional Frequency Breakdown (top 5 emotions with counts) / Implication: lead with pain or desire?

**SECTION 10 ,  VISUAL & SENSORY LANGUAGE**
For AI image generation prompts. All verbatim.
- Scene Descriptions (minimum 8): [quote] → [scene type]
- Color & Texture Language (minimum 6 phrases)
- Mood & Atmosphere Language (minimum 6 phrases)
- Before-State Visual Descriptions (minimum 5): what the problem looks like
- After-State Visual Descriptions (minimum 5): what success looks like
- Lifestyle & Identity Markers (minimum 5)

**SECTION 11 ,  COMPETITIVE LANDSCAPE**
Each of 3 competitors: customer sentiment (verbatim quotes), Ad Library observations, what customers say they do well, what they fail at. Then: Sea of Sameness (3, 5 angles to avoid) / Unoccupied Positioning Territory (the gap).

**SECTION 12 ,  FEATURE-TO-BENEFIT TRANSLATION**
Table: Feature | Customer Benefit (customer language) | Emotional Benefit | Source Quote. Minimum 10 rows. Benefit columns must use verbatim customer language.

**SECTION 13 ,  LANGUAGE & MESSAGING GOLDMINE**
Raw customer language only. No analysis. No paraphrase.
- Words used to describe the problem (minimum 12)
- Words used to describe the desired solution (minimum 12)
- Highest emotional intensity phrases (minimum 10 with source)
- "I wish..." and "Why can't they..." statements (minimum 6)
- Before/After language pairs (minimum 6 ,  both sides verbatim)
- Anti-ad language (minimum 5): how customers describe fake or annoying brands
- Identity language (minimum 6): how customers describe themselves
- Problem-space language (minimum 8): raw unfiltered language from Phase 6 ,  words people use about the problem before they've heard of any product

**SECTION 14 ,  SOCIAL PROOF ARSENAL**
Top 10 most quotable testimonials (verbatim, source, intensity, awareness level). Note: [Direct Brand] or [Competitor: name] for each. Key publicly available metrics. Skeptic-to-Believer quotes (minimum 3).

**SECTION 15 ,  VALUE EQUATION ANALYSIS**
Dream Outcome (verbatim evidence) / Perceived Likelihood (belief/doubt quotes) / Time Delay (speed/patience quotes) / Effort & Sacrifice (ease-of-use quotes). Strategic finding: strongest lever and weakest lever.

**SECTION 16 ,  SOURCE INDEX**
Every URL visited, by platform, with one-line note on what was found. Close with Data Richness Assessment: what percentage came from direct brand vs. competitor VOC vs. problem-space, and what that means for the document's applicability.

---

## QUALITY CHECKLIST

Before completing research:

- [ ] Minimum 40 verbatim quotes total
- [ ] At least 6 different source platforms
- [ ] At least 8 quotes tagged [Problem-Space]
- [ ] At least 5 quotes tagged [Competitor: name]
- [ ] Every quote has source attribution, intensity tag, awareness tag, JTBD Force tag
- [ ] Competitor quotes tagged [Competitor: Brand Name]
- [ ] Problem-space quotes tagged [Problem-Space]
- [ ] No quote has been paraphrased or cleaned up
- [ ] ICP paragraph written in Section 2
- [ ] Section 10 contains only true visual/sensory language
- [ ] Section 13 Language Goldmine contains only verbatim customer phrases
- [ ] Section 11 includes actual Ad Library observations
- [ ] Data Richness Assessment noted in header and Section 16
- [ ] No hooks or headlines written anywhere ,  this is raw research only
