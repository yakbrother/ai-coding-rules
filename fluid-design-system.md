# Fluid Design System Ruleset v3.0

**Purpose**: This document serves as both a human-readable design philosophy AND machine-readable instructions for AI coding assistants (like Claude). When building web interfaces, follow these principles to create fluid, accessible, and modern experiences.

**Author**: Tim (@yakbrother)  
**Last Updated**: September 30, 2025  
**Conformance Targets**: WCAG 2.2 AA, EU AI Act, Modern CSS Standards

---

## Philosophy: Why This Approach?

Modern web design has moved beyond fixed breakpoints and rigid grid systems. The web is fundamentally fluid - it exists on devices from 320px watches to 4K monitors. Fighting this fluidity creates more work and worse experiences.

**Core Principle**: Let the browser do the heavy lifting. Use algorithms and intrinsic sizing instead of predetermined scales.

This approach embraces what [Utopia](https://utopia.fyi) calls "elegant scaling without breakpoints" - instead of designing for arbitrary breakpoints, we define minimum and maximum scales and let the browser interpolate between them based on viewport width. This creates a **systematic approach to fluidity** where designers and developers can work from the same mental model.

### The Three Priorities

**1. Fluidity First** - Content and context should determine layout, not arbitrary pixel values.

**2. Visual Hierarchy** - Not all elements deserve equal attention. Use contrast, size, and spacing deliberately.

**3. Comfortable Reading** - Typography should disappear. Readers shouldn't think about the text presentation.

---

## For AI Assistants: Instructions

When generating code for web interfaces, ALWAYS follow these rules. They represent tested patterns that create better user experiences with less code.

### Decision Framework

Use this flowchart when choosing layout approaches:

```
Does the layout need a fixed number of columns?
├─ YES → Use .repeating-grid or .repeating-flex
└─ NO → Should content determine column count?
    ├─ YES → Use .fluid-grid (PREFERRED)
    └─ NO → Explain why fixed columns are needed
```

### Critical "NEVER" Rules

These are absolute. If asked to violate these, push back and explain why:

- **NEVER use fixed pixel breakpoints** (`@media (min-width: 768px)`) - Use container queries or fluid units
- **NEVER use generic divs** when semantic HTML exists - `<article>`, `<section>`, `<nav>` have meaning
- **NEVER omit alt text** on images - Accessibility is non-negotiable
- **NEVER trap keyboard focus** - All interactive elements must be keyboard-accessible
- **NEVER use color alone** to convey information - Use icons, labels, or patterns too
- **NEVER set line-height in pixels** - Always use unitless values (e.g., `1.5`)
- **NEVER use `!important`** unless overriding third-party CSS you can't modify

---

## CSS Architecture

All styles must be organized using CSS Cascade Layers for predictable specificity management.

### Layer Structure

```css
@layer reset, theme, layout, components, utilities;
```

**Why layers?** They prevent specificity wars. Lower layers (reset) can't override higher layers (utilities) regardless of selector specificity. This makes CSS predictable.

#### Layer Purposes

**`reset`** - Normalize browser defaults
```css
@layer reset {
  *, *::before, *::after {
    box-sizing: border-box;
  }
  
  body {
    margin: 0;
    line-height: 1.5;
  }
}
```

**`theme`** - Global design tokens (colors, spacing, typography scales)

**Pro Tip**: Use [Utopia's Fluid Type Scale Calculator](https://utopia.fyi/type/calculator/) and [Fluid Space Calculator](https://utopia.fyi/space/calculator/) to generate your own optimized clamp() values. These tools help you define min/max scales that interpolate smoothly across all viewport sizes.

```css
@layer theme {
  :root {
    /* Fluid Spacing - grows with viewport */
    /* Generated using https://utopia.fyi/space/calculator/ */
    --space-3xs: clamp(0.25rem, 0.23rem + 0.11vw, 0.31rem);
    --space-2xs: clamp(0.5rem, 0.46rem + 0.22vw, 0.63rem);
    --space-xs: clamp(0.75rem, 0.68rem + 0.33vw, 0.94rem);
    --space-s: clamp(1rem, 0.91rem + 0.43vw, 1.25rem);
    --space-m: clamp(1.5rem, 1.37rem + 0.65vw, 1.88rem);
    --space-l: clamp(2rem, 1.83rem + 0.87vw, 2.5rem);
    --space-xl: clamp(3rem, 2.74rem + 1.3vw, 3.75rem);
    --space-2xl: clamp(4rem, 3.65rem + 1.74vw, 5rem);
    --space-3xl: clamp(6rem, 5.48rem + 2.61vw, 7.5rem);
    
    /* One-up pairs for consistent spacing relationships */
    --space-3xs-2xs: clamp(0.25rem, 0.14rem + 0.54vw, 0.63rem);
    --space-2xs-xs: clamp(0.5rem, 0.37rem + 0.65vw, 0.94rem);
    --space-xs-s: clamp(0.75rem, 0.59rem + 0.76vw, 1.25rem);
    --space-s-m: clamp(1rem, 0.74rem + 1.3vw, 1.88rem);
    --space-m-l: clamp(1.5rem, 1.2rem + 1.52vw, 2.5rem);
    --space-l-xl: clamp(2rem, 1.48rem + 2.61vw, 3.75rem);
    --space-xl-2xl: clamp(3rem, 2.39rem + 3.04vw, 5rem);
    --space-2xl-3xl: clamp(4rem, 2.96rem + 5.22vw, 7.5rem);
    
    /* Simplified aliases for common use */
    --space-default: var(--space-m);
    --space-compact: var(--space-s);
    --space-section: var(--space-xl-2xl);
    
    /* Fluid Typography - readable across all sizes */
    /* Generated using https://utopia.fyi/type/calculator/ */
    --font-size-xs: clamp(0.75rem, 0.7rem + 0.26vw, 0.94rem);
    --font-size-sm: clamp(0.89rem, 0.83rem + 0.28vw, 1.06rem);
    --font-size-base: clamp(1rem, 0.95rem + 0.25vw, 1.25rem);
    --font-size-md: clamp(1.13rem, 1.06rem + 0.33vw, 1.38rem);
    --font-size-lg: clamp(1.27rem, 1.17rem + 0.47vw, 1.63rem);
    --font-size-xl: clamp(1.42rem, 1.29rem + 0.65vw, 1.91rem);
    --font-size-2xl: clamp(1.6rem, 1.42rem + 0.87vw, 2.25rem);
    --font-size-3xl: clamp(1.8rem, 1.57rem + 1.15vw, 2.66rem);
    --font-size-4xl: clamp(2.03rem, 1.73rem + 1.52vw, 3.16rem);
    --font-size-5xl: clamp(2.28rem, 1.89rem + 1.96vw, 3.75rem);
    
    /* Layout Configuration */
    --layout-fluid-min: 35ch; /* Optimal reading line length */
    --layout-content-max: 70ch; /* Maximum readable line length */
    
    /* Colors - Use HSL for easier adjustments */
    --color-primary: hsl(220 90% 56%);
    --color-surface: hsl(0 0% 100%);
    --color-text: hsl(0 0% 10%);
  }
}
```

**Why fluid scales?** Traditional design systems use fixed values (e.g., `16px`, `24px`, `32px`) that jump at breakpoints. Fluid scales use `clamp()` to smoothly interpolate between minimum and maximum values, eliminating the need for most breakpoints while maintaining proportional relationships across all screen sizes.

**`layout`** - Reusable structural utilities (see Layout System below)

**`components`** - Specific UI components (buttons, cards, forms)

**`utilities`** - Single-purpose overrides (text-align, visibility helpers)

---

## Layout System: Fluid Utilities

These classes form the foundation of all layouts. They're framework-independent and work with any content.

### Primary Choice: Fluid Grid

**Class**: `.fluid-grid`

**When to use**: Whenever you DON'T know exactly how many columns you need. This is the default choice.

**Why it works**: The browser calculates optimal column count based on available space and minimum item width. No breakpoints needed.

```css
@layer layout {
  .fluid-grid {
    display: grid;
    gap: var(--layout-default-gap, 3vmax);
    grid-template-columns: repeat(
      auto-fit,
      minmax(min(var(--_fluid-min, var(--layout-fluid-min)), 100%), 1fr)
    );
  }
}
```

**How it works**:
- `auto-fit` creates as many columns as can fit
- `minmax()` sets minimum column width but allows growth
- `min()` prevents overflow on small screens
- Local variable `--_fluid-min` allows per-instance overrides

**Example usage**:
```html
<!-- Default: items are at least 35ch wide -->
<div class="fluid-grid">
  <article>Card 1</article>
  <article>Card 2</article>
  <article>Card 3</article>
</div>

<!-- Override: items are at least 250px wide -->
<div class="fluid-grid" style="--_fluid-min: 250px">
  <img src="product1.jpg" alt="Product 1">
  <img src="product2.jpg" alt="Product 2">
  <img src="product3.jpg" alt="Product 3">
</div>
```

### Alternative: Fluid Flex

**Class**: `.fluid-flex`

**When to use**: When you want items to shrink below their minimum size if needed (rare).

```css
@layer layout {
  .fluid-flex {
    display: flex;
    flex-wrap: wrap;
    gap: var(--layout-default-gap, 3vmax);
  }
  
  .fluid-flex > * {
    flex: 1 1 var(--_fluid-min, var(--layout-fluid-min));
  }
}
```

### Secondary Choices: Fixed Columns

Only use these when you have a specific design reason for fixed column counts.

**Class**: `.repeating-grid`

**When to use**: Gallery with exactly 3 columns, dashboard with 2 equal panels, etc.

```css
@layer layout {
  .repeating-grid {
    display: grid;
    gap: var(--layout-default-gap, 3vmax);
    grid-template-columns: repeat(var(--_grid-repeat, 2), 1fr);
  }
}
```

**Example**:
```html
<!-- Always 4 equal columns -->
<div class="repeating-grid" style="--_grid-repeat: 4">
  <div>Col 1</div>
  <div>Col 2</div>
  <div>Col 3</div>
  <div>Col 4</div>
</div>
```

### Stack Layouts

**Class**: `.stack`

**When to use**: Vertical content flow with consistent spacing (articles, form fields, card content).

**Why it's useful**: Eliminates margin collapsing issues and provides predictable spacing.

```css
@layer layout {
  .stack {
    display: flex;
    flex-direction: column;
    gap: var(--_stack-gap, var(--layout-default-gap));
  }
}
```

### Advanced: Container Queries

Make layout items responsive to their container size, not viewport size.

```css
@layer layout {
  .fluid-grid {
    container-type: inline-size;
  }
  
  .fluid-grid > * {
    container-name: grid-item;
  }
}

/* Item responds to its own width, not viewport */
@container grid-item (min-width: 400px) {
  .card-title {
    font-size: var(--font-size-large);
  }
}
```

---

## Typography: Comfortable Reading

**Core principle**: "Readers read best what they read most." Typography should be invisible - comfortable enough that users don't notice it.

### Rules

1. **Line Length**: Between 45-75 characters (20-35em) for body text
   - **Why**: Longer lines cause readers to lose their place. Shorter lines force too many eye movements.

2. **Line Height**: Inversely proportional to font size
   - Small text (12-16px): `line-height: 1.6-1.8`
   - Body text (16-20px): `line-height: 1.5`
   - Large headings (32px+): `line-height: 1.1-1.3`
   - **Why**: Large text with tall line-height looks disconnected. Small text needs breathing room.

3. **Font Size**: Use fluid scales, not fixed sizes
   ```css
   /* Good - scales smoothly between min and max */
   font-size: clamp(1rem, 0.95rem + 0.25vw, 1.25rem);
   
   /* Bad - jumps at arbitrary breakpoint */
   font-size: 16px;
   @media (min-width: 768px) { font-size: 18px; }
   ```

4. **Measure** (line length constraint):
   ```css
   .prose {
     max-width: var(--layout-content-max, 70ch);
     margin-inline: auto; /* Center the content */
   }
   ```

5. **Hanging Punctuation** (Progressive Enhancement):
   The `hanging-punctuation` property extends punctuation marks (like opening quotes) outside the text block for cleaner visual alignment. Currently Safari-only, but it's a perfect example of progressive enhancement.
   
   ```css
   .prose {
     hanging-punctuation: first last;
   }
   
   /* Fix: Prevent quotes from disappearing in form fields */
   input, textarea {
     hanging-punctuation: none;
   }
   ```
   
   **Why it matters**: This creates professional-looking typography with clean edges, especially in pull quotes and article intros. The property degrades gracefully in browsers that don't support it.
   
   **Gotcha**: Without the form field fix, quotation marks at the start of input values will be pushed outside the field boundary and hidden. [Credit: Jeremy Keith](https://adactio.com/journal/21027)

### Hierarchy Through Contrast

Don't just make headings bigger - create contrast through multiple properties:

```css
/* Primary heading - size + weight + spacing */
h1 {
  font-size: var(--font-size-xl);
  font-weight: 700;
  line-height: 1.1;
  letter-spacing: -0.02em; /* Tighter for large text */
}

/* Body text - comfortable default */
p {
  font-size: var(--font-size-base);
  line-height: 1.6;
  color: var(--color-text);
}

/* Secondary text - de-emphasized through color */
.text-secondary {
  color: hsl(from var(--color-text) h s calc(l + 30%));
  font-size: 0.875em;
}
```

---

## Visual Hierarchy: Directing Attention

Not all elements deserve equal prominence. Use these techniques to create clear hierarchy:

### 1. Size and Weight

Primary actions should be immediately obvious:
```css
/* Primary button - largest, highest contrast */
.btn-primary {
  padding: 1em 2em;
  font-weight: 600;
  background: var(--color-primary);
  color: white;
}

/* Secondary button - outlined, less prominent */
.btn-secondary {
  padding: 1em 2em;
  border: 2px solid currentColor;
  background: transparent;
}

/* Tertiary action - styled like a link */
.btn-tertiary {
  padding: 0.5em 1em;
  text-decoration: underline;
  background: none;
}
```

### 2. Color and Contrast

**De-emphasize rather than only emphasizing**:
```css
/* Instead of only making primary content bold... */
.important { font-weight: 700; }

/* ...also make secondary content softer */
.metadata {
  color: hsl(from var(--color-text) h s calc(l + 40%));
  font-size: 0.875em;
}
```

### 3. Spacing

More space = more importance:
```css
.section-major {
  margin-block: 5vmax; /* Large gaps separate major sections */
}

.section-minor {
  margin-block: 2vmax; /* Smaller gaps for related content */
}
```

---

## Accessibility (Non-Negotiable)

These rules are legal requirements in many jurisdictions and ethical requirements everywhere.

### WCAG 2.2 AA Compliance

**Perceivable**:
- All images must have `alt` text describing their purpose
- Videos must have captions and audio descriptions
- Color contrast must meet 4.5:1 for text, 3:1 for UI components

```html
<!-- Good -->
<img src="chart.png" alt="Sales increased 23% in Q3 compared to Q2">

<!-- Bad -->
<img src="chart.png" alt="chart">
<img src="chart.png"> <!-- Missing alt entirely -->
```

**Operable**:
- All functionality must work with keyboard only
- Focus indicators must be visible (never `outline: none` without replacement)
- No content flashes more than 3 times per second

```css
/* Good - visible focus indicator */
button:focus-visible {
  outline: 3px solid var(--color-primary);
  outline-offset: 2px;
}

/* Bad - removes focus with no replacement */
button:focus {
  outline: none;
}
```

**Understandable**:
- Set page language: `<html lang="en">`
- Form inputs must have associated labels
- Error messages must be in text, not just color

```html
<!-- Good -->
<label for="email">Email address</label>
<input type="email" id="email" name="email" required>

<!-- Bad -->
<input type="email" placeholder="Email"> <!-- Placeholder is not a label -->
```

**Robust**:
- Use semantic HTML: `<button>`, `<nav>`, `<main>`, `<article>`
- Custom components need ARIA: `role`, `aria-label`, `aria-expanded`, etc.

```html
<!-- Good - semantic HTML -->
<button type="button">Click me</button>

<!-- Bad - div pretending to be a button -->
<div onclick="handleClick()">Click me</div>

<!-- Acceptable if you add ARIA -->
<div role="button" tabindex="0" onclick="handleClick()" onkeypress="handleClick()">
  Click me
</div>
```

### EU AI Act Compliance

When using AI-generated content or AI systems:

1. **Transparency**: Always disclose AI-generated content
   ```html
   <p>
     <img src="ai-generated-image.png" alt="...">
     <small>Image generated by AI</small>
   </p>
   ```

2. **No Prohibited Practices**: Never use AI for:
   - Social scoring
   - Subliminal manipulation
   - Exploiting vulnerabilities (children, disabilities)

3. **High-Risk Systems**: If AI makes decisions about employment, education, or credit:
   - Human oversight required
   - Explainability (XAI) required
   - Data governance and logging required

---

## Practical Examples

### Example 1: Blog Post Layout

```html
<article class="stack prose">
  <header class="stack" style="--_stack-gap: 1rem">
    <p class="text-secondary">
      <time datetime="2025-09-30">September 30, 2025</time>
    </p>
    <h1>Fluid Design Systems</h1>
    <p class="text-large">Why fixed breakpoints are obsolete</p>
  </header>
  
  <div class="stack" style="--_stack-gap: 1.5rem">
    <p>Traditional responsive design relies on media queries...</p>
    <p>But modern CSS gives us better tools...</p>
    
    <figure>
      <img src="comparison.png" alt="Side-by-side comparison showing fixed grid breaking at 768px versus fluid grid adapting smoothly">
      <figcaption>Fluid grids adapt continuously, not at arbitrary breakpoints</figcaption>
    </figure>
  </div>
</article>
```

### Example 2: Dashboard Layout

```html
<div class="repeating-grid" style="--_grid-repeat: 3; --layout-default-gap: 2rem">
  <section class="card">
    <h2>Revenue</h2>
    <p class="metric">$1.2M</p>
    <p class="change positive">+23% vs last month</p>
  </section>
  
  <section class="card">
    <h2>Active Users</h2>
    <p class="metric">12,450</p>
    <p class="change positive">+5% vs last month</p>
  </section>
  
  <section class="card">
    <h2>Churn Rate</h2>
    <p class="metric">2.1%</p>
    <p class="change negative">-0.3% vs last month</p>
  </section>
</div>
```

### Example 3: Product Grid

```html
<!-- Automatically creates 2-5 columns based on space -->
<div class="fluid-grid" style="--_fluid-min: 250px">
  <article class="product-card">
    <img src="product1.jpg" alt="Ergonomic keyboard with backlit keys">
    <h3>Pro Keyboard</h3>
    <p class="price">$129</p>
    <button class="btn-primary">Add to Cart</button>
  </article>
  
  <!-- More products... -->
</div>
```

---

## Common Mistakes to Avoid

### ❌ Using Fixed Breakpoints for Layout

```css
/* Bad - arbitrary breakpoints create maintenance burden */
.grid {
  display: grid;
  grid-template-columns: 1fr;
}

@media (min-width: 640px) {
  .grid { grid-template-columns: 1fr 1fr; }
}

@media (min-width: 1024px) {
  .grid { grid-template-columns: 1fr 1fr 1fr 1fr; }
}
```

```css
/* Good - browser calculates optimal columns */
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(min(250px, 100%), 1fr));
  gap: 2rem;
}
```

### ❌ Pixel-Perfect Spacing

```css
/* Bad - precise but fragile */
.card {
  padding: 24px;
  margin-bottom: 32px;
}

@media (min-width: 768px) {
  .card {
    padding: 32px;
    margin-bottom: 48px;
  }
}
```

```css
/* Good - fluid and maintainable */
.card {
  padding: clamp(1.5rem, 2vw, 3rem);
  margin-bottom: var(--space-default);
}
```

### ❌ Ignoring Content Width

```css
/* Bad - text becomes unreadable on wide screens */
.article {
  width: 100%;
}
```

```css
/* Good - constrain for readability */
.article {
  max-width: 70ch;
  margin-inline: auto;
}
```

---

## Troubleshooting Common Issues

### When `aspect-ratio` Doesn't Work

The `aspect-ratio` property is powerful for maintaining proportions in fluid layouts, but certain conditions can break it. Here's what to watch for:

**Problem 1: Both dimensions are set**
```css
/* Bad - aspect-ratio is ignored */
.box {
  width: 300px;
  height: 200px;
  aspect-ratio: 16 / 9; /* Has no effect */
}

/* Good - let aspect-ratio calculate one dimension */
.box {
  width: 300px;
  aspect-ratio: 16 / 9; /* Height automatically becomes ~169px */
}
```

**Problem 2: Content forces height**
```css
/* Bad - tall content breaks aspect-ratio */
.card {
  width: 300px;
  aspect-ratio: 1; /* Tries to be square */
}
/* But if content is taller than 300px, the box stretches */

/* Good - constrain content or allow overflow */
.card {
  width: 300px;
  aspect-ratio: 1;
  overflow: auto; /* Or use a scrollable container */
}
```

**Problem 3: Flexbox stretching**
```css
/* Bad - flex stretch overrides aspect-ratio */
.flex-container {
  display: flex;
  align-items: stretch; /* Default value */
}
.flex-item {
  aspect-ratio: 16 / 9; /* Gets stretched anyway */
}

/* Good - prevent stretching */
.flex-item {
  aspect-ratio: 16 / 9;
  align-self: flex-start; /* Or center, flex-end */
}
```

[Reference: Chris Coyier's "Things That Can Break aspect-ratio"](https://frontendmasters.com/blog/things-that-can-break-aspect-ratio-in-css/)

---

## Testing & Validation

### Testing HTML with CSS

Modern CSS selectors are powerful enough to audit your HTML for accessibility issues, bloat, and usability problems without JavaScript. This technique is especially useful when you don't have direct access to a client's codebase - just provide a test stylesheet.

**Example: Detecting accessibility issues**
```css
/* Flag images without alt text */
img:not([alt]) {
  outline: 5px solid red;
}

/* Flag links that open in new windows without warning */
a[target="_blank"]:not([aria-label]):not([title])::after {
  content: " ⚠️ Opens in new window";
  color: red;
}

/* Flag empty headings */
h1:empty, h2:empty, h3:empty, h4:empty, h5:empty, h6:empty {
  outline: 5px solid orange;
}

/* Flag inputs without associated labels */
input:not([type="hidden"]):not([aria-label]):not([id]) {
  outline: 5px solid red;
}

/* Or check if ID doesn't have a matching label */
input[id]:not([type="hidden"]) {
  outline: 3px solid orange;
}
input[id]:not([type="hidden"]):has(+ label[for]) {
  outline: none; /* ID has a matching label, all good */
}
```

**Example: Detecting HTML bloat**
```css
/* Flag excessive div nesting */
div > div > div > div > div {
  outline: 3px dashed purple;
}

/* Flag non-semantic markup */
div[class*="header"],
div[class*="footer"],
div[class*="nav"],
div[class*="article"] {
  outline: 3px solid blue;
}
div[class*="header"]::before,
div[class*="footer"]::before,
div[class*="nav"]::before,
div[class*="article"]::before {
  content: "Consider semantic HTML: <header>, <footer>, <nav>, <article>";
  background: blue;
  color: white;
  display: block;
  padding: 0.5em;
}
```

**Benefits of CSS testing**:
- Visual regression detection in the browser inspector
- No JavaScript or build process required
- Easy to share with clients as a single stylesheet
- Works well with visual debugging workflows

[Reference: Heydon Pickering's "Testing HTML With Modern CSS"](https://heydonworks.com/article/testing-html-with-modern-css/)

### Browser Testing Checklist

Before considering any interface complete, verify:

- [ ] Resize browser from 320px to 3840px - layout never breaks
- [ ] Navigate entire interface using only keyboard (Tab, Enter, Space, Arrows)
- [ ] Turn off CSS - content still makes sense and is in logical order
- [ ] Run axe DevTools or similar accessibility scanner - zero violations
- [ ] Test with screen reader (NVDA, JAWS, or VoiceOver)
- [ ] Check color contrast with browser DevTools or Contrast Checker
- [ ] View in forced colors mode (Windows High Contrast)
- [ ] Test with 200% browser zoom - no horizontal scrolling
- [ ] Apply test stylesheet to check for HTML issues

---

## Advanced Techniques

### Self-Modifying CSS Variables (Experimental)

**Warning**: This technique is experimental and should not be used in production yet. It's included here for educational purposes and future-proofing.

The CSS spec doesn't allow a custom property to reference itself directly. However, using container-style queries, we can create variables that access their previous state - useful for cyclic values or nested contexts.

**Use case: Cycling through hues without static lists**
```css
@layer theme {
  :root {
    --hue: 0;
  }
}

.color-cycle {
  container-name: cycle;
  --hue: 200; /* Initial hue */
}

/* Each nested level shifts hue by 60 degrees */
@container style(--hue: 0) {
  .color-cycle {
    --hue: 60;
    background: hsl(60 70% 60%);
  }
}

@container style(--hue: 60) {
  .color-cycle {
    --hue: 120;
    background: hsl(120 70% 60%);
  }
}

@container style(--hue: 120) {
  .color-cycle {
    --hue: 180;
    background: hsl(180 70% 60%);
  }
}
/* Continue pattern... */
```

**Use case: Matching border-radius visually**
```css
.card {
  --radius: 8px;
  border-radius: var(--radius);
}

/* Inner elements automatically get smaller radius */
@container style(--radius: 8px) {
  .card-inner {
    --radius: 4px;
    border-radius: var(--radius);
  }
}
```

**Why it's useful**: This pattern enables dynamic theming, nested components with progressive adjustments, and algorithmic design systems without JavaScript.

[Reference: Roman Komarov's "Self-Modifying Variables"](https://kizu.dev/self-modifying-variables/)  
[Original CSSWG proposal for inherit()](https://github.com/w3c/csswg-drafts/issues/2864)

### Container Queries for Component-Level Responsiveness

Make layout items respond to their own size, not the viewport size. This creates truly modular components.

```css
/* Card responds to its container, not viewport */
.card {
  container: card / inline-size;
  padding: var(--space-s);
}

/* When card is at least 400px wide, use horizontal layout */
@container card (min-width: 400px) {
  .card {
    display: grid;
    grid-template-columns: 200px 1fr;
    gap: var(--space-m);
  }
  
  .card-image {
    grid-row: 1 / -1;
  }
}

/* When card is at least 600px wide, increase spacing */
@container card (min-width: 600px) {
  .card {
    padding: var(--space-l);
    gap: var(--space-l);
  }
  
  .card-title {
    font-size: var(--font-size-2xl);
  }
}
```

**Benefits**:
- Components work in any context (sidebar, main content, modal)
- No media queries needed for component variations
- True encapsulation - components don't know about page layout

**Automatic container setup for grid items**:
```css
.fluid-grid > * {
  container: grid-item / inline-size;
}

/* Now any card in a fluid grid is automatically responsive */
@container grid-item (min-width: 350px) {
  .card { /* styles */ }
}
```

### Subgrid with Auto-Counting Rows

Use the `:has()` selector to automatically calculate the number of subgrid rows based on content:

```css
.subgrid-rows {
  /* Automatically set --subgrid-rows based on child count */
  &:has(> :nth-child(1):last-child) { --subgrid-rows: 1; }
  &:has(> :nth-child(2):last-child) { --subgrid-rows: 2; }
  &:has(> :nth-child(3):last-child) { --subgrid-rows: 3; }
  &:has(> :nth-child(4):last-child) { --subgrid-rows: 4; }
  &:has(> :nth-child(5):last-child) { --subgrid-rows: 5; }
  &:has(> :nth-child(6):last-child) { --subgrid-rows: 6; }
  
  > * {
    display: grid;
    grid-row: auto / span var(--subgrid-rows, 4);
    grid-template-rows: subgrid;
    gap: var(--subgrid-gap, var(--space-s));
  }
}
```

**How it works**: The selector `&:has(> :nth-child(3):last-child)` reads as "if the 3rd child is also the last child, then there are exactly 3 children." This sets the subgrid row count automatically without manual configuration.

---

## References and Further Reading

### Core Concepts
- **Utopia Fluid Design**: [utopia.fyi](https://utopia.fyi) - Philosophy and calculators for fluid type and space scales
- **Utopia Type Calculator**: [utopia.fyi/type/calculator](https://utopia.fyi/type/calculator/) - Generate fluid typography scales
- **Utopia Space Calculator**: [utopia.fyi/space/calculator](https://utopia.fyi/space/calculator/) - Generate fluid spacing scales
- **Modern CSS Layouts**: [web.dev/learn/css/layout](https://web.dev/learn/css/layout) - Comprehensive guide to CSS layout

### Layout Systems
- **Modern CSS Layouts, No Framework Needed**: [Smashing Magazine](https://www.smashingmagazine.com/2024/05/modern-css-layouts-no-framework-needed/) - Geoff Graham's utility class approach
- **Every Layout**: [every-layout.dev](https://every-layout.dev) - Algorithmic layout patterns by Heydon Pickering and Andy Bell
- **CSS Grid Level 3 (Subgrid)**: [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_grid_layout/Subgrid)

### Accessibility & Standards
- **WCAG 2.2 Quick Reference**: [w3.org/WAI/WCAG22/quickref](https://www.w3.org/WAI/WCAG22/quickref/)
- **Testing HTML with Modern CSS**: [Heydon Pickering](https://heydonworks.com/article/testing-html-with-modern-css/)
- **EU AI Act**: [eur-lex.europa.eu](https://eur-lex.europa.eu)

### Typography & Visual Design
- **On Web Typography**: Jason Santa Maria's book on readable web text
- **Refactoring UI**: Visual design principles by Steve Schoger and Adam Wathan
- **Hanging Punctuation Gotcha**: [Jeremy Keith](https://adactio.com/journal/21027)

### Advanced CSS Techniques
- **Useful CSS Tips and Techniques**: [Smashing Magazine](https://www.smashingmagazine.com/2024/06/css-tips-and-techniques/)
- **Self-Modifying Variables**: [Roman Komarov](https://kizu.dev/self-modifying-variables/)
- **CSS Container Queries**: [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_container_queries)
- **Things That Break aspect-ratio**: [Chris Coyier](https://frontendmasters.com/blog/things-that-can-break-aspect-ratio-in-css/)
- **Cascade Layers Primer**: [Andy Bell](https://piccalil.li/blog/a-primer-on-the-cascade-and-specificity)
- **Getting Started With Cascade Layers**: [Stephanie Eckles](https://www.smashingmagazine.com/2022/01/introduction-css-cascade-layers/)

---

**Version History**:
- v3.1 (2025-09-30): Added Utopia fluid scales, hanging-punctuation, CSS testing techniques, aspect-ratio troubleshooting, self-modifying variables, expanded references
- v3.0 (2025-09-30): Added AI assistant instructions, expanded examples, clarified "why"
- v2.0 (2025-08): Initial fluid design system
- v1.0 (2024): Bootstrap-based system (deprecated)

**License**: CC BY-SA 4.0 - Use freely, attribute, share improvements

---

*This document lives at stack.yakbrother.dev and is continuously refined based on real-world usage.*
