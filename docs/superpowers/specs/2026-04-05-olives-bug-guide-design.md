# Olive's Guide to Sandy Bugs by Malcolm — Design Spec

## Context

Olive (age 7) is scared of bugs. Her cousin Malcolm (age 10) wants to help her get over that fear through a progressive scavenger hunt featuring 18 real bugs found in Sandy, Oregon. The app builds confidence by starting with friendly, harmless bugs and gradually unlocking scarier ones as Olive finds real bugs outside.

This is a gift from Malcolm to Olive — his voice and personality are the soul of the app.

## Approach

Single HTML file (`index.html`) with inline CSS and JS. No build tools, no frameworks, no external dependencies. Vanilla JS, CSS custom properties, localStorage for persistence. Hosted on Vercel/GitHub Pages — used on an iPad.

## File Structure

```
~/Projects/olives-bug-guide/
├── index.html          # The entire app
└── docs/               # Design docs
```

## Data Model

### Bug Data (JS array, hardcoded)

```js
{ id, name, malcolmsTake, whereToLook, safety, tier }
```

- `id`: 1-18
- `safety`: "safe" | "look" | "distance"
- `tier`: 1 | 2 | 3

18 bugs total. Data is static — defined as a const array in the script.

### Persisted State (localStorage)

Key: `olives-bugs`

```json
{
  "1": { "found": true, "foundAt": "2026-04-06T14:30:00Z" },
  "5": { "found": true, "foundAt": "2026-04-06T15:12:00Z" }
}
```

Only found bugs are stored. Absence = not found.

### Tier Unlock Logic

- **Tier 1** (bugs 1-6): Always unlocked
- **Tier 2** (bugs 7-16): Unlocks when `totalFound >= 3`
- **Tier 3** (bugs 17-18): Unlocks when `totalFound >= 8`

## Layout (top to bottom, single scroll page)

### 1. Header (sticky)
- Title: "Olive's Guide to Sandy Bugs"
- Subtitle: "by Malcolm"
- Fixed at top on scroll

### 2. Progress Bar (sticky, below header)
- "X / 18 bugs found" with animated fill bar
- Next milestone hint: "Find 3 bugs to unlock Spiders & Middle Tier!"
- Updates dynamically as bugs are found and tiers unlock

### 3. Welcome Message (conditional)
- Shows when 0 bugs found
- Malcolm's intro: "Hey Olive! I made this guide for you. Start with the friendly bugs — they're totally harmless. The more you find, the more you unlock. You got this."
- Collapses/hides once first bug is found

### 4. Tier Sections (x3)
Each tier section contains:
- **Tier header**: Name + badge (e.g., "Friendly Starters")
- **Card grid**: 3 columns (iPad has the space)
- **Locked state**: Grayed overlay, lock icon, "Find X more bugs to unlock!" Cards visible but dimmed + blurred so Olive can see there's more to discover

### 5. Bug Cards (collapsed, in grid)
- Silhouette placeholder: circular with generic bug outline shape (CSS/inline SVG), `?` for undiscovered
- Bug name
- Safety badge (color-coded pill)
- Checkmark overlay if found
- Tap to expand

### 6. Bug Card (expanded, modal)
- Centered overlay at ~70% viewport width (iPad)
- Backdrop blur on background
- Content:
  - Bug name (large)
  - Silhouette placeholder (larger)
  - Malcolm's take (main text, styled distinctly — left border or subtle background)
  - "Where to look" with location pin icon
  - Safety level badge
  - Big "Found it!" button (or "Already found!" state with a small "Undo" text link — not a full button, to prevent accidental resets)
  - X button / tap outside to close

### 7. Found Celebration
On tapping "Found it!":
- CSS confetti/stars particle animation (keyframe-based colored circles)
- Random Malcolm quote overlay:
  - "Nice find, Olive!"
  - "You're getting brave!"
  - "That's X bugs found!"
  - "You're a real bug hunter!"
  - "Told you it wasn't scary!"
  - "Sandy bugs don't stand a chance!"
- Auto-dismisses after ~2 seconds
- If this find triggers a tier unlock: special "New tier unlocked!" glow animation after confetti

## Visual Design

### Color Palette
| Role | Color | Hex |
|------|-------|-----|
| Background | Warm cream | `#FFF8F0` |
| Primary | Soft sage green | `#7BA05B` |
| Accent | Goldenrod yellow | `#E8A838` |
| Alert | Soft terracotta | `#D4654A` |
| Border/text accent | Warm brown | `#8B6F47` |
| Body text | Dark brown | `#3D2E1F` |

### Typography
- Font stack: `-apple-system, 'Nunito', sans-serif` (rounded, friendly)
- Base size: 18px+ (readable for a 7-year-old, outdoors on iPad)
- Headings: bold, large
- Malcolm's voice: styled with a subtle left border or tinted background block to feel like "his words"

### Safety Badges (color-coded pills)
- **Safe to touch**: Green pill + hand icon
- **Look but don't touch**: Yellow pill + eye icon
- **Respect from a distance**: Orange pill + warning icon

### Tier Color Coding
- Tier 1 (Friendly): Green tint/border
- Tier 2 (Spiders & Middle): Yellow/amber tint/border
- Tier 3 (Boss Level): Orange/red tint/border — feels elevated, not scary

### Bug Silhouettes
- Simple CSS circles or inline SVG with generic bug outline shape
- Found: full color fill
- Unfound: gray outline with `?`
- Locked tier: dimmed + CSS `filter: blur(2px)` + reduced opacity

### Animations
- **Card expand**: Scale up from card position, backdrop blur behind modal
- **Found it**: CSS particle burst — small colored circles fly outward via `@keyframes`
- **Tier unlock**: Border glow pulse + cards slide/fade in
- **Progress bar**: Smooth `width` transition on update

## iPad Considerations
- **Grid**: 3 columns default, works in portrait and landscape
- **Modal**: ~70% width centered, not full-screen takeover — feels like a field guide page
- **Touch targets**: Minimum 44px, generous card padding
- **Orientation**: CSS grid auto-adapts; no orientation lock needed
- **Outdoor readability**: High contrast text on light background, large fonts

## Offline Behavior
- Single HTML file, no external fetches — works offline after first load
- localStorage persists across sessions
- No service worker needed (no assets to cache beyond the single file)

## What's NOT in v1
- Custom bug illustrations (silhouette placeholders for now)
- Sound effects
- Bug journal / history view
- Share progress feature
- Dark mode
- Login / accounts

## Verification

1. Open `index.html` on an iPad (or iPad simulator / responsive mode)
2. Confirm Tier 1 bugs are visible and tappable, Tiers 2-3 are locked but teased
3. Tap a bug card — modal opens with all fields (name, Malcolm's take, where to look, safety badge)
4. Tap "Found it!" — confetti animation plays, Malcolm quote shows, card updates to found state
5. Find 3 bugs — Tier 2 unlocks with animation
6. Find 8 bugs total — Tier 3 unlocks
7. Reload the page — all found state persists via localStorage
8. Progress bar reflects correct count and next milestone at every step
