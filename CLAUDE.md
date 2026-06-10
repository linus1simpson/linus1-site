# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a single-page personal site for linus1.com ("The Lounge"). The entire site lives in one file: `index.html` (≈3,500 lines). There is no build system, bundler, or package manager — edit the file directly and open it in a browser to preview.

## Running locally

Open `index.html` directly in a browser, or serve it:

```sh
python3 -m http.server 8080
```

Some features (Supabase guestbook, art ratings, pull-up answers, visitor tracking) require network access to Supabase; they will fail silently on a plain file:// URL due to CORS.

## Architecture

### Single-file structure

All HTML, CSS (in `<style>`), and JavaScript (in `<script>`) is inline in `index.html`. The three `.mp3` files (`freefall.mp3`, `yuknowxo.mp3`, `time-today.mp3`) are local audio assets used by the music booth/vinyl player.

### External dependencies (CDN only)

- **Supabase JS v2** — backend for guestbook, visitor names, art ratings, pull-up answers
- **Google Tag Manager** (GA4 tag `G-XT0THRSTMS`)
- **Google Fonts** — Cormorant Garamond, IBM Plex Mono, Fraunces

### Supabase tables

| Table | Purpose |
|---|---|
| `visitors` | Stores visitor names entered at the entrance |
| `guestbook` | Guestbook sign entries |
| `art_ratings` | Like/dislike votes on gallery pieces (`piece_name`, `liked`) |
| `pullup_answers` | User responses to Pull Up section questions |

The client is initialized at the top of the `<script>` block as `_sb`.

### Design system

CSS custom properties defined in `:root` use an ocean palette:
- `--navy` / `--navy2` / `--navy3` — deep blue backgrounds
- `--forest` / `--forest2` / `--forest3` — teal/green accents
- `--crimson` / `--crim2` — red accent
- `--gold` / `--gold2` / `--brass` — gold accent
- `--orange` / `--orange2` — CTA orange
- `--white`, `--off`, `--parch` — light backgrounds
- `--ink`, `--muted`, `--dim` — text colors

Three typefaces: `'Cormorant Garamond'` (headings), `'IBM Plex Mono'` (labels, kickers, UI), `'Fraunces'` (body).

Button variants (`.btn-forest`, `.btn-crimson`, `.btn-orange`, `.btn-navy`, `.btn-gold`, `.btn-outline`) use a tactile 3D stacked box-shadow system with an active press effect via `translateY(5px)`.

### Sections and key features

The page flows through these `<section id="...">` regions, in order:

1. **Entrance overlay** (`#entrance`) — animated robot "lbot" that must be clicked to enter; captures visitor name and saves to Supabase + `localStorage`
2. **Home** (`#home`) — hero with an animated CSS vinyl turntable; clicking the platter plays/pauses local MP3 audio
3. **Sound / Booth** (`#sound`) — SoundCloud iframe widget + paginated track list; synced play state with the hero turntable
4. **Maze** (`#maze-section`) — canvas path-following game; completing it unlocks `#vault`
5. **Vault** (`#vault`) — hidden section (revealed after maze), contains bonus tracks
6. **Scantron** (`#scantron-section`) — 5-question quiz; passing score unlocks `#vault2`
7. **Vault 2** (`#vault2-wrapper`) — second hidden section
8. **Arcade** (`#arcade`) — canvas Pong game; beating it unlocks `#vault3`
9. **Vault 3** (`#vault3-wrapper`) — third hidden section
10. **Gallery** (`#gallery`) — 12-column CSS grid of canvas-rendered artworks with tag filtering and a lightbox
11. **Art Rate** (`#artrate`) — Tinder-style swipe cards for rating gallery pieces; results saved to Supabase
12. **Dispatch** (`#dispatch`) — editorial cards that open an in-page modal overlay
13. **Pull Up** (`#pullup`) — prompt cards; visitors submit text responses saved to/loaded from Supabase
14. **Footer** — newsletter subscribe + guestbook sign form

### Scroll-reveal

Elements with class `.reveal` animate in (`opacity`, `translateY`) when they enter the viewport via an `IntersectionObserver` that adds class `.in`.
