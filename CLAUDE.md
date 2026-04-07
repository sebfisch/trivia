# Trivia Pursuit

A single-file mobile web app (`docs/index.html`, ~850 lines) served via GitHub Pages that replaces Trivial Pursuit card decks using the [Open Trivia DB API](https://opentdb.com).

## What it does

- 6 colour-coded category buttons (matching classic Trivial Pursuit: blue=Geography, pink=Film, yellow=History, brown=Art, green=Science, purple=Sports)
- Difficulty selector: Easy / Medium / Hard / Any
- Tap a colour → fetches a multiple-choice question from OTDB
- Answer options are shown; tap one to mark a selection, then "Reveal Answer" highlights correct (green) / wrong-tapped (red)
- Settings screen lets players remap any colour to any OTDB category; assignments persist in `localStorage`

## Architecture

Everything lives in `docs/index.html` — no build tools, no external dependencies, no separate CSS/JS files.

- **CSS** inline in `<style>`: dark theme, CSS custom properties, `100dvh` mobile layout, max-width 480px
- **3 screens**: `#screen-home`, `#screen-question`, `#screen-settings` — toggled via `.active` class
- **State**: plain JS object; category assignments saved to `localStorage` key `tp-categories`
- **OTDB session token**: cached in `localStorage` key `tp-session-token` with a 6 h TTL to avoid repeat questions

## Key design decisions & bugs fixed

- **Shuffling**: answers are shuffled so the correct answer isn't always button #1. Positional answers ("None of the above", "All of the above", etc.) are always placed last after shuffling — `isPositional()` helper in the JS.
- **Token reset bug**: `resetToken()` now checks `data.response_code === 0` from the OTDB reset endpoint before accepting the result; previously it accepted any HTTP-200, storing an invalid token and causing the generic "Could not load question" error when returning to the app after >6 h.

## Development branch

`claude/trivia-pursuit-app-xlR8b` (GitHub Pages is served from `main`/`docs/` — merge this branch to deploy)
