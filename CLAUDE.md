# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Cocos2d-js web game** deployed as a **Cloudflare Worker**. The game is a fruit merging puzzle (Suika-style) where fruits drop and combine into larger fruits.

## Deployment

- **Platform**: Cloudflare Workers
- **Config**: `wrangler.jsonc`
- **Build**: Static files served via Cloudflare's edge network (compatibility_date: 2026-05-14)
- **Asset serving**: All files in root directory served as static assets

## Key Files

| File | Purpose |
|------|---------|
| `main.js` | Cocos2d-js bootstrap - initializes engine, loads settings, starts game |
| `index.html` | Entry point - loads cocos engine, ads.js, and main.js |
| `ads.js` | Ad management (Google IMA SDK integration) |
| `src/project.js` | Minified game bundle (~229KB) - contains all game logic |
| `src/settings.js` | Cocos engine config - scene list, asset registry, collision matrix |
| `src/extraSettings.js` | **Modifiable game settings** - fruit options, score multiplier, web title |

## Game Configuration (extraSettings.js)

This file contains user-configurable options that affect gameplay:

```javascript
extraScore = 1          // Score multiplier
wuDi = true             // Invincible mode
firstFruit = 0          // First fruit type (0-10, 0=grape, 9=half watermelon)
reverseLevelUp = false  // Reverse merge direction
minRandomFruitNum = 0   // Min random fruit (0-10)
maxRandomFruitNum = 5   // Max random fruit (1-11)
fruitQTan = false       // Bounciness (0-1)
fruitSlowDown = false   // Fall resistance (>0)
clickChangeFruit = false // Click icon to change fruit
adLink = '...'          // Ad redirect URL
selectModal = false     // Score selection modal
```

## Architecture

```
index.html → main.js → src/project.js
                ↓
         src/settings.js (scene: "db://assets/Scene/MainGameScene.fire")
                ↓
         res/import/ (asset UUIDs mapped in settings.js)
```

The game uses Cocos Creator's scene format (`.fire` files) and an asset import system with UUID-based resource loading.

## Local Development

No build step required - it's pure static files. To test locally:

```bash
npx wrangler dev    # Start Cloudflare Workers dev server
```

Or simply open `index.html` in a browser (though ad features may not work locally).

## Cloudflare Deployment

```bash
npx wrangler deploy   # Deploy to Cloudflare Workers
```