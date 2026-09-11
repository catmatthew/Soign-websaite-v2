---
name: brand-style-reference
description: Reference library of real-world brand design systems (colors, typography, spacing, components, tone) for 74+ companies such as Stripe, Airbnb, Apple, Linear, and Figma. Use when generating or restyling a UI and the user wants it to match, or take inspiration from, a specific brand's visual language, or when picking concrete design tokens (palette, type scale, spacing) grounded in a real product rather than invented from scratch.
---

# Brand Style Reference

This skill bundles the `design-md` dataset from
[VoltAgent/awesome-design-md](https://github.com/VoltAgent/awesome-design-md)
(MIT licensed) — a collection of `DESIGN.md` documents, one per brand,
each describing that brand's design system in plain text: color palette,
typography, spacing scale, component patterns, motion, and voice/tone.

## When to use this skill

- The user asks for a UI "in the style of X" (e.g. "make this look like Stripe").
- You need concrete, real-world design tokens instead of guessing at a
  palette or type scale.
- You want to compare how several brands solve the same UI problem
  (e.g. pricing tables, empty states, form validation).

## How to use it

1. Look in `design-md/` for a folder matching the brand name the user
   mentioned (e.g. `design-md/stripe/DESIGN.md`).
2. Read that brand's `DESIGN.md` (and `README.md` if present) to extract
   the relevant tokens: color values, font families/scale, spacing units,
   border radii, shadow style, and any stated design principles.
3. Apply those tokens directly in the generated code/CSS — do not
   paraphrase them into something vaguer. Cite which brand's system you
   pulled from so the user can verify.
4. If no exact brand match exists, pick the closest analog (e.g. another
   fintech or another minimalist SaaS product) and say so explicitly
   rather than silently substituting.

## Available brands

Run `ls design-md/` to see the full current list (74 at time of import),
including: airbnb, apple, bmw, cal, claude, coinbase, dell-1996,
elevenlabs, expo, ferrari, figma, and many more — each folder name is the
brand's design token directory.

## Attribution

Source: https://github.com/VoltAgent/awesome-design-md (MIT License).
Content is derived from publicly observable brand styling and is provided
as reference material, not official brand guidelines.
