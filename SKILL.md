---
name: website-builder
description: >
  Use this skill whenever the user asks to build a website, landing page, or HTML page.
  Before writing any code, the agent MUST collect all required inputs by asking a structured
  set of questions. Only after receiving answers should the agent proceed to generate the page.
---

# Website Builder Skill

This skill guides the creation of a complete, production-ready HTML page (or multi-page site)
based on inputs gathered from the user through a structured intake conversation.

---

## STEP 1 — Intake (MANDATORY before writing any code)

**Do NOT generate any HTML, CSS, or code before completing this step.**

When this skill is triggered, immediately ask the user the following questions in a single message.
Present them clearly and numbered:

---

> Before I build your website, I need a few details:
>
> **1. Brand / Product name** — What's the brand or product name for this site?
>
> **2. Logo** — Do you have a logo file (SVG or PNG preferred) I can use?
>    If yes, provide the file path or paste the SVG code.
>    If no, should I generate a text-based logo or a placeholder icon?
>
> **3. Accent / Primary color** — What is your primary accent color?
>    Provide a hex code (e.g. `#7c3aed`) or describe it and I'll suggest options.
>
> **4. Background color / theme** — Do you want a dark background, light background, or a specific color?
>    Dark backgrounds tend to look best for modern tech sites.
>
> **5. Pages / Sections** — What pages or sections do you need?
>    (e.g. Home, About, Services, Contact — or paste the full list)
>
> **6. Vibe / Aesthetic** — What is the overall feel you're going for?
>    Examples: premium tech, luxury, playful, minimal, bold, corporate, futuristic, elegant.
>
> **7. Content source** — Do you have an existing website I can pull copy from?
>    If yes, share the URL. If no, paste the text/copy you want on the page,
>    or I will generate placeholder content based on the brand name and vibe.
>
> **8. Language / Direction** — Is the site Arabic (RTL), English (LTR), or bilingual?
>
> **9. Video or media** — Do you have a background video or hero image file ready?
>    If yes, provide the file path or URL.

---

## STEP 2 — Confirmation Summary

After the user responds, produce a short confirmation block before coding:

```
✅ Here's what I'll build:
- Brand: [name]
- Colors: Primary [hex], Background [hex or description]
- Pages/Sections: [list]
- Vibe: [aesthetic]
- Language: [LTR / RTL / Bilingual]
- Logo: [file / text-based / placeholder]
- Content: [user-provided / AI-generated]
- Media: [none / file path / URL]

Proceeding to build...
```

If anything critical is missing (brand name, accent color, or page list), ask only for those
missing items before proceeding. Do not ask all questions again.

---

## STEP 3 — Build the Page

Generate a complete, single-file HTML page (HTML + CSS + JS in one file) unless the user
explicitly requests separate files.

### Technical Requirements

- **Structure**: Semantic HTML5 (`<header>`, `<main>`, `<section>`, `<footer>`)
- **Styling**: Inline `<style>` block using CSS custom properties (variables) for all colors
- **Responsive**: Mobile-first, fully responsive using CSS Grid and Flexbox
- **RTL Support**: If Arabic or bilingual, add `dir="rtl"` on the `<html>` tag and use
  logical CSS properties (`margin-inline-start`, `padding-inline-end`, etc.)
- **Fonts**: Load from Google Fonts. Choose fonts appropriate to the vibe — avoid Inter,
  Roboto, Arial. For Arabic, use Almarai or Tajawal.
- **No external frameworks** unless user requests Bootstrap, Tailwind, etc.

### Color System

Define all colors as CSS variables at the top of the stylesheet:

```css
:root {
  --color-accent:     /* user's primary color */;
  --color-bg:         /* background */;
  --color-surface:    /* card/panel background, slightly lighter/darker than bg */;
  --color-text:       /* primary text */;
  --color-text-muted: /* secondary text */;
  --color-border:     /* subtle borders */;
}
```

### Logo Handling

- **SVG provided**: Inline the SVG directly in the `<header>`
- **PNG path provided**: Use `<img src="[path]" alt="[brand] logo">`
- **No logo**: Generate a stylized text logo using the brand name with the accent color,
  optionally adding a simple geometric SVG icon beside it

### Design Guidelines

Follow the aesthetic direction from the vibe answer:

| Vibe         | Typography style        | Layout style              | Animation style          |
|--------------|-------------------------|---------------------------|--------------------------|
| Premium tech | Sharp geometric sans    | Full-bleed, grid-heavy    | Subtle fade-in on scroll |
| Luxury       | Serif + thin sans       | Centered, generous space  | Slow reveal, parallax    |
| Playful      | Rounded, expressive     | Asymmetric, colorful      | Bouncy, hover effects    |
| Minimal      | Single clean sans       | Lots of whitespace        | Minimal or none          |
| Bold         | Heavy display font      | Large hero, high contrast | Fast transitions         |
| Corporate    | Professional sans       | Traditional columns       | Subtle hover states      |
| Futuristic   | Monospace / sci-fi font | Dark, neon accents        | Glitch, scan-line        |

Apply: scroll-triggered fade-in animations, hover effects on buttons/cards, and a smooth
scroll behavior. Use one hero animation that feels deliberate and polished.

### Sections to include (default, adapt based on user input)

1. **Navigation bar** — logo, nav links, CTA button
2. **Hero section** — headline, subheadline, CTA button(s), optional background video/image
3. **Features / Services** — icon + title + description cards
4. **About / Story** — brand story or mission statement
5. **Call to Action** — bold section encouraging the user to take action
6. **Footer** — logo, links, copyright, social icons (if relevant)

---

## STEP 4 — Delivery

After generating the HTML file:

1. Save it to `/mnt/user-data/outputs/index.html`
2. Call `present_files` with the output path so the user can download it
3. Add a short summary (2–3 sentences max) describing what was built and any key design choices

Do NOT write a long explanation of every section — the user can read the file.

---

## Edge Cases

- **User provides a URL for content**: Use `web_fetch` to retrieve the page text, then
  extract headings, descriptions, and copy to use in the generated page.
- **User wants multiple pages**: Generate `index.html`, `about.html`, etc. as separate files
  and present all of them together.
- **User wants React**: Produce a `.jsx` single-file React component instead, using Tailwind
  utility classes for styling.
- **User wants bilingual (Arabic + English)**: Add a language toggle button in the nav that
  switches between LTR and RTL using a `data-lang` attribute on `<html>`, swapping all text
  content with a small JS dictionary object.
