# Bug Fix: Blank Screen on Tournament Selection

## Problem
The application displayed a blank screen with no player cards rendered. The header, search bar, and filter legend loaded, but the player grid was completely empty regardless of search input or tournament selection.

## Root Cause
Commit `3789fad` ("Add tournament selection with Players Championship 2026 data") restructured the player database and seed data into a `TOURNAMENTS` object but **accidentally removed 7 essential global variable/constant declarations** that the rest of the application depends on:

| Variable | Purpose |
|----------|---------|
| `TIER_C` | Tier color hex codes (elite, high, medium, low) used for card styling |
| `TIER_CLS` | Tier CSS class names for card badges |
| `TIER_LBL` | Tier display labels ("ELITE EV", "HIGH EV", etc.) |
| `FILTERS` | Array of filter button labels |
| `liveMap` | Object storing live scoring data keyed by player name |
| `lastUpd` | Timestamp of last data update |
| `activeFlt` | Currently active filter (defaults to "All") |

## Why It Caused a Blank Screen
1. On page load, `renderAll()` is called
2. `renderGrid()` calls `filtered()` which references `activeFlt` — **undefined, throws ReferenceError**
3. JavaScript execution halts entirely
4. No player cards are ever rendered — blank screen

## Fix
Re-added all 7 missing declarations after the `TOURNAMENTS` object and before the active tournament management section in `index.html` (line 566).

## File Changed
- `index.html` — Added missing `TIER_C`, `TIER_CLS`, `TIER_LBL`, `FILTERS`, `liveMap`, `lastUpd`, and `activeFlt` declarations
