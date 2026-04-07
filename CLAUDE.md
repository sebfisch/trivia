# Trivia Pursuit

A single-file mobile web app (`docs/index.html`, ~900 lines) served via GitHub Pages that replaces Trivial Pursuit card decks using the [The Trivia API](https://the-trivia-api.com).

## What it does

- 6 colour-coded category buttons (matching classic Trivial Pursuit: blue=Geography, pink=Film & TV, yellow=History, brown=Arts & Literature, green=Science, purple=Sports)
- Difficulty selector: Easy / Medium / Hard / Any
- Tap a colour → fetches a multiple-choice question from The Trivia API
- Answer options are shown; tap one to mark a selection, then "Reveal Answer" highlights correct (green) / wrong-tapped (red)
- Settings screen lets players select a language and remap any colour to any TTA category; both persist in `localStorage`

## Architecture

Everything lives in `docs/index.html` — no build tools, no external dependencies, no separate CSS/JS files.

- **CSS** inline in `<style>`: dark theme, CSS custom properties, `100dvh` mobile layout, max-width 480px
- **3 screens**: `#screen-home`, `#screen-question`, `#screen-settings` — toggled via `.active` class
- **State**: plain JS object; category assignments saved to `localStorage` key `tp-categories`; selected language saved to `tp-language`
- **No session tokens**: The Trivia API free tier does not support deduplication sessions; repeat questions are accepted

## API: The Trivia API

Endpoint: `GET https://the-trivia-api.com/v2/questions?limit=1&categories=<slug>&difficulties=<level>&languages=<code>`

- Response fields: `question.text`, `correctAnswer`, `incorrectAnswers[]`, `category` (slug), `difficulty`
- 10 categories (static, no network fetch needed): `science`, `history`, `geography`, `sports`, `music`, `arts_and_literature`, `film_and_tv`, `society_and_culture`, `general_knowledge`, `food_and_drink`
- 7 languages: `en`, `es`, `fr`, `de`, `nl`, `tr`, `hi`
- Free tier, non-commercial use (CC BY-NC 4.0)

## Language selection

- Flag pill buttons in the Settings screen (`#lang-row`, rendered by `renderLanguageRow()`)
- Changing language resets colour→category assignments to TTA defaults and saves both to `localStorage`
- `<html lang="">` is updated dynamically for accessibility

## localStorage migration

Old `tp-categories` entries stored OTDB numeric IDs. On load, `loadCategories()` detects any numeric value and discards the saved data, falling back to TTA slug defaults. The legacy `tp-session-token` key is removed on startup.

## Key design decisions & bugs fixed

- **Shuffling**: answers are shuffled so the correct answer isn't always button #1. Positional answers ("None of the above", "All of the above", etc.) are always placed last after shuffling — `isPositional()` helper in the JS.
- **Token reset bug** (historical): when the app used Open Trivia DB, `resetToken()` checked `data.response_code === 0` before accepting the result to avoid storing an invalid token.

## Development branch

`claude/trivia-pursuit-app-xlR8b` (GitHub Pages is served from `main`/`docs/` — merge this branch to deploy)
