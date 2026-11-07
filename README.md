# Frozens Stream Essentials — standalone

A fresh, self-contained Fabric mod for Minecraft **1.21.11**. No license, no key, no account, no server — it
runs fully offline. This is an original rebuild: it does **not** download a menu from anywhere and does not
depend on the old backend.

Mod id: `moneytracker` · package: `com.moneytracker` (kept the same so your existing
`config/moneytracker.json` still works).

---

## What's in this build

**Working and tested (pure-logic core, verified in this project's own tests):**

- **Money tracker engine.** Reads server chat and keeps a running total — pay in/out, buying, selling and
  delivering orders, with `k/m/b/t` amounts and commas. All the patterns from your previous build are carried
  over and unit-tested (`MoneyEngine` has a `main` that runs the test cases).
- **Config system.** Every setting and default recovered from your old jar, saved to `config/moneytracker.json`.
  Old config files keep working; the license fields are simply gone.

**Written and wired, compiles with the mod (needs Minecraft to build, see below):**

- **Money HUD** — the "Live | +$9.1B" bar, with size, position (3×3 anchor + offset), colours, label,
  `$`/`+` signs, compact vs full numbers, shadow and background.
- **F9 settings screen** — a working menu where every control edits the config live and saves on change.
- **Chat listener, F9 keybind, HUD renderer** — the Fabric glue.
- All your **textures, fonts and icon** are bundled under `src/main/resources/assets/`.

**Not yet ported (next steps — see Roadmap):** the glass/themed multi-page menu look, and the extra HUD
modules (Spotify, info panel, keystrokes, armor, auction helper, crosshair, block outline, enchant glint,
custom sky). Their **settings are all preserved** in the config; what's left is drawing them. I didn't fake
these — the menu you have here is plain but real, and the themed layer goes on top of it.

---

## Building the jar

This needs a machine with internet (to download Minecraft, Fabric and the mappings once). I couldn't produce
the compiled jar in my sandbox because it has no access to those servers — but the project is set up to build
in one command.

```bash
cd frozens-stream-essentials
./gradlew build        # Windows: gradlew.bat build
```

The finished mod lands in `build/libs/frozens-stream-essentials-3.0.0.jar`. Drop it in your `mods/` folder
alongside **Fabric API**. (`build/libs/` will also hold a `-sources.jar` — that one is not the mod.)

To run it in a dev client without installing: `./gradlew runClient`.

### Updating versions

The four lines at the top of `gradle.properties` are the only things tied to a Minecraft version. If a build
complains it can't find `yarn_mappings`, `loader_version` or `fabric_version`, open
<https://fabricmc.net/develop>, pick 1.21.11, and copy the exact values it shows into `gradle.properties`.
I filled in current-looking values, but that page is the source of truth.

### One file to watch

`src/main/java/com/moneytracker/hud/MoneyHud.java` uses the 1.21.11 matrix API
(`context.getMatrices().pushMatrix()/scale()`). If your mappings differ and only that file fails to compile,
the comment at the top of it says exactly what to change. Everything else uses long-stable Fabric APIs.

---

## Project layout

```
src/main/java/com/moneytracker/
  Config.java            all settings + JSON load/save (no Minecraft types)
  Auction.java           auction helper data
  engine/
    Amounts.java         parse/format money ("9.1B" <-> 9_100_000_000)
    MoneyEngine.java     chat -> total; run it directly to see the tests pass
  client/
    MoneyTrackerClient.java  entry point: config, keybind, chat, HUD
    Tracker.java             engine <-> config bridge, HUD label/colour
  hud/MoneyHud.java      draws the money bar
  menu/MoneyScreen.java  F9 settings screen
src/main/resources/
  fabric.mod.json
  assets/moneytracker/   icon, textures, sounds, lang
  assets/minecraft/font/ bundled fonts (+ their licenses in /licenses)
```

## Roadmap (what I'd build next)

1. Port the themed glass UI (sidebar, cards, toggles, themes, backgrounds) as a renderer layer over the
   existing `MoneyScreen` controls, plus the multi-page structure and search.
2. Add the other HUD modules one at a time (Spotify, info panel, keystrokes, armor, info bubble).
3. Auction helper screen, and the world-render features (block outline, enchant glint, custom sky, crosshair).
4. The Configs page (save/load/share) on top of the standalone config.

The tested money engine and the config are the foundation everything else hangs off, which is why they came
first.
