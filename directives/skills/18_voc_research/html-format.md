# HTML Formatting Instructions ,  VOC Research Document

Take the complete VOC research report from Phase 1 and render it as a professional, downloadable HTML document. Build it as a single self-contained HTML file with no external dependencies ,  no CDN links, no external fonts, no external scripts.

---

## Design System

**Colors:**
- Primary navy: `#162441`
- Secondary slate: `#8A9BBC`
- Background: `#FFFFFF`
- Surface light: `#F4F6FA`
- Surface mid: `#E8EDF5`
- Pain/Push accent: `#C0392B` (muted red)
- Desire/Pull accent: `#1A6B3C` (muted green)
- Anxiety accent: `#8B6914` (muted amber)
- Habit accent: `#5B4A8A` (muted purple)
- Unaware tag: `#6B7A99`
- Problem-Aware tag: `#C0392B`
- Solution-Aware tag: `#1A5276`
- Product-Aware tag: `#1A6B3C`
- Most-Aware tag: `#162441`

**Typography:** System sans-serif stack: `-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif`
- Section labels: 11px uppercase, letter-spacing 0.12em, `#8A9BBC`
- Section titles: 22px bold, `#162441`
- Body text: 15px, line-height 1.75, `#2C3E50`
- Quote text: 14px, font-style italic, `#162441`, line-height 1.7
- Quote source: 11px, `#8A9BBC`
- Tag pills: 11px, bold, uppercase, letter-spacing 0.08em

---

## Document Structure

### Cover Header
Full-width dark navy (`#162441`) header block. Contains:
- Product name as large white headline (28px bold)
- Research date, product URL, total quotes collected, sources searched ,  as a clean stat row in slate
- Awareness level distribution shown as a small horizontal bar/pill row

### Sticky Sidebar Navigation
Fixed left sidebar (220px wide) visible on scroll. Links to all 15 sections. Highlight active section in navy with left border accent. On mobile, hide sidebar and add a top sticky nav bar instead. Section titles in sidebar should be short labels: "Executive Summary," "Product Snapshot," "Pain Points," etc.

### Main Content Area
Left-padded to clear the sidebar (240px). Max-width 820px. Generous vertical spacing between sections (80px).

Each section has:
- Section number in slate (e.g. "01")
- Section title in bold navy
- Section content
- Thin divider line below

---

## Component Styles

### Verbatim Quote Blocks
This is the most important visual element. Every verbatim customer quote must use this treatment:

```
┌─ 3px left border in JTBD Force color ──────────────────────────┐
│  "Exact quote text in italic, 14px, navy"                       │
│                                                                  │
│  Platform · Product/Thread · Date    [INTENSITY] [AWARENESS]   │
└──────────────────────────────────────────────────────────────────┘
```

- Left border color maps to JTBD Force: Push = red, Pull = green, Anxiety = amber, Habit = purple
- Background: `#F4F6FA`
- Padding: 16px 20px
- Border-radius: 4px
- Source line: 11px, `#8A9BBC`
- Tags rendered as colored pill badges inline with source line
- "Copy" button floated top-right: small, slate, shows "✓ Copied" for 2 seconds on click
- Implement copy with: `navigator.clipboard.writeText(quoteText)`

**Intensity badge colors:**
- Low: `#BDC3C7` background, white text
- Medium: `#F39C12` background, white text
- High: `#E67E22` background, white text
- Extreme: `#C0392B` background, white text

**Awareness badge colors:** Use the awareness level colors from the design system above.

### Tag/Pill Lists (Language Goldmine, Power Phrases)
Two-column pill layout. Each pill:
- Background: `#E8EDF5`
- Border-radius: 20px
- Padding: 6px 14px
- Font: 13px, `#162441`
- "Copy" button appears on hover

### Feature-to-Benefit Table
Clean striped table:
- Header row: `#162441` background, white text, 12px uppercase
- Alternating rows: `#FFFFFF` and `#F4F6FA`
- Cell padding: 12px 16px
- Customer benefit and emotional benefit columns use slightly larger text to signal priority

### Ideal Customer Profile Section (Section 2)
The ICP paragraph gets a prominent callout box ,  dark navy background, white text, slightly larger font (16px). This is the "north star" of the document. Below it, render Situation, Identity Language, Online Hangouts, and Search Language as four clean labeled cards in a 2×2 grid. Identity language phrases and search phrases rendered as pill tags with copy buttons.

### JTBD Force Cards (Section 7)
Four force cards in a 2×2 grid:
- Push card: left border red, light red background tint
- Pull card: left border green, light green background tint
- Anxiety card: left border amber, light amber background tint
- Habit card: left border purple, light purple background tint

Each card contains the Job Statement, then the supporting verbatim quotes below it.

### Emotional Territory Map (Section 8)
Emotion frequency breakdown rendered as a simple horizontal bar chart using pure CSS (no JS charts). Each emotion gets a bar proportional to its frequency count. Color bars from high (red) to low (slate).

### Awareness Level Breakdown (Section 7)
Each awareness level as a collapsible card (HTML `<details>`/`<summary>`) with the level name, percentage estimate, and quotes inside. Collapsed by default except the dominant level.

### Visual Direction Cards (Section 9)
Sub-categories (Scene Descriptions, Color & Texture, Mood & Atmosphere, etc.) rendered as labeled card groups. Each entry shows the verbatim quote, then an arrow (→), then the extracted label or scene type. Light teal left-border to distinguish this section visually.

### Social Proof Cards (Section 13)
Each of the top 10 testimonials as a card. Quote large (16px), source small below. Skeptic-to-Believer quotes get a special badge: "CONVERTED SKEPTIC" in amber.

### Competitive Landscape (Section 10)
Each competitor in a collapsible section. "Sea of Sameness" angles rendered as red-tinted warning pills. "Unoccupied Territory" rendered in a prominently styled green callout box.

### Value Equation (Section 14)
Four quadrant layout (2×2 grid) for the four levers. Each quadrant labeled, with supporting quotes below. Weakest lever gets an amber warning badge, strongest lever gets a green strength badge.

---

## Interactive Elements

### Copy Button (Universal)
Every quote block, every power phrase pill, every language goldmine phrase must have a copy-to-clipboard button. Implementation:

```javascript
function copyToClipboard(text, btn) {
  navigator.clipboard.writeText(text).then(() => {
    const original = btn.innerHTML;
    btn.innerHTML = '✓ Copied';
    btn.style.color = '#1A6B3C';
    setTimeout(() => { btn.innerHTML = original; btn.style.color = ''; }, 2000);
  });
}
```

### Collapsible Sections
Use native `<details>`/`<summary>` for all collapsible elements. Style the summary element to look like a clickable header, not a raw disclosure triangle.

### Active Section Highlight
Implement with IntersectionObserver to highlight the current section in the sidebar nav:

```javascript
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      document.querySelectorAll('.nav-link').forEach(l => l.classList.remove('active'));
      document.querySelector(`.nav-link[href="#${entry.target.id}"]`)?.classList.add('active');
    }
  });
}, { threshold: 0.3 });
document.querySelectorAll('section[id]').forEach(s => observer.observe(s));
```

### Filter Bar (Sections 3, 4, 5)
For Pain Points, Desires, and Objections sections, add a filter bar above the quotes:
- Filter by JTBD Force: All | Push | Pull | Anxiety | Habit
- Filter by Intensity: All | High | Extreme
- Filter by Awareness: All | Unaware | Problem-Aware | Solution-Aware | Product-Aware | Most-Aware
- Implement with data attributes: `data-force`, `data-intensity`, `data-awareness` on each quote block
- JavaScript toggles `display: none` on non-matching quote blocks

---

## Document-Level Features

**Quick Stats Bar:** Immediately below the cover header, a horizontal stats strip showing: Total Quotes | Sources Searched | Dominant Awareness Level | Primary Emotion | Primary Pain Point (name only). All in one scannable row.

**Section anchors:** Every section must have an `id` attribute matching its sidebar nav link.

**Print styles:** Include a `@media print` block that hides the sidebar, removes interactive elements, and ensures quote blocks print cleanly.

**Self-contained:** All styles in a `<style>` block in `<head>`. All JavaScript in a `<script>` block before `</body>`. No external dependencies whatsoever.

---

## Section Rendering Order

Render sections in this exact order ,  this matches the copywriter's workflow priority:

1. Cover Header + Quick Stats Bar
2. Executive Summary (01)
3. Language & Messaging Goldmine (13) ,  moved up because this is what copywriters reach for first
4. Visual & Sensory Language (10) ,  moved up to serve image generation workflow
5. Ideal Customer Profile (02) ,  who this person is and where they live online
6. Customer Pain Points & Frustrations (04)
7. Customer Desires & Dream Outcomes (05)
8. Objections & Purchase Anxieties (06)
9. Emotional Territory Map (09)
10. Awareness Level Deep Dive (08)
11. Jobs to Be Done Analysis (07)
12. Feature-to-Benefit Translation (12)
13. Social Proof Arsenal (14)
14. Competitive Landscape (11)
15. Value Equation Analysis (15)
16. Product & Brand Snapshot (03)
17. Source Index (16)

The sidebar nav should reflect this rendering order, not the original numbering.

---

## Quality Check Before Saving

Before outputting the HTML file, verify:

- [ ] File is fully self-contained ,  no `<link>` tags to external CSS, no external `<script src>` tags
- [ ] Every verbatim quote has a copy button
- [ ] Left border colors on quotes correctly map to JTBD Force
- [ ] Intensity and awareness badges render on every quote
- [ ] Sidebar nav links correctly to all 17 sections (including ICP and Source Index)
- [ ] Filter bars work on Pain Points, Desires, and Objections sections
- [ ] Awareness level distribution shown in cover header
- [ ] Language Goldmine and Visual Language sections are in positions 3 and 4 (after Executive Summary)
- [ ] Document renders cleanly without horizontal scroll on a 1280px viewport
