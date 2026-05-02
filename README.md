# Megastructures - No Per-System Limit

A Stellaris mod that removes the per-system build restrictions on all megastructures, allowing unlimited construction of Dyson Spheres, Ring Worlds, Matter Decompressors, and more within a single star system.

## What This Mod Does (Technically)

Vanilla Stellaris enforces one-per-system limits through two primary mechanisms in `common/megastructures/*.txt`:

1. **`possible` blocks** — These contain triggers like `has_megastructure = no` that prevent construction if any megastructure already exists in the system.
2. **`country_megastructure_build_cap_add`** — A country modifier that artificially caps how many megastructures an empire can build.

This mod overrides the vanilla megastructure definitions by mirroring the game's folder hierarchy and strips those restrictions while leaving all other requirements intact:

- Technology prerequisites
- Resource costs (alloys, influence, etc.)
- Construction time
- Ascension perk requirements
- Location validation (must be built around stars, etc.)

## Project Structure

```
NoLimitPerSystem/
  descriptor.mod              # Mod metadata (no 'path' key)
  common/
    megastructures/
      00_ring_world.txt
      01_dyson_sphere.txt
      02_spy_orb.txt
      03_think_tank.txt
      06_matter_decompressor.txt
      07_strategic_coordination_center.txt
      08_mega_art_installation.txt
      09_interstellar_assembly.txt
      11_mega_shipyard.txt
      13_quantum_catapult.txt
      17_orbital_arc_furnace.txt
      18_dyson_swarm.txt
      20_grand_archive.txt
  thumbnail.png               # 512x512 Steam Workshop thumbnail
```

Each `.txt` file overrides a vanilla megastructure entry by key name (e.g., `dyson_sphere_0`, `ring_world_1`). The mod removes `country_megastructure_build_cap_add` modifiers and strips `possible` blocks that enforce `has_megastructure = no` checks.

## Target Game Version

**v4.3***

The mod should remain compatible with future minor patches unless Paradox significantly restructures megastructure definitions.

## Compatibility Notes

- **Safe to add mid-game** — This mod only removes restrictions; it does not add new mechanics or script triggers.
- **Load order** — Generally irrelevant for this mod. Since it overwrites vanilla megastructure definitions, place it below other megastructure mods if you want their changes to take priority.
- **Conflicts** — Any other mod that edits the same megastructure files will conflict. If you use multiple megastructure overhaul mods, check for overlapping file names in `common/megastructures/`.

## Installation (Development)

1. Copy or symlink the `NoLimitPerSystem/` folder into your Stellaris user mod directory:
   ```
   %USERPROFILE%\Documents\Paradox Interactive\Stellaris\mod\
   ```
2. Ensure `NoLimitPerSystem.mod` (the descriptor with `path=mod/NoLimitPerSystem`) exists in that same `mod/` folder.
3. Launch Stellaris via the Paradox Launcher and enable the mod.
4. Check `Documents\Paradox Interactive\Stellaris\logs\error.log` after startup for script errors.

## File Encoding Rules

Stellaris is strict about encodings:

| File type | Required encoding |
|-----------|-------------------|
| `.txt` (scripts, defines, megastructures, etc.) | UTF-8 |
| `.mod` / `descriptor.mod` | UTF-8 |
| `.gfx` | UTF-8 **(not BOM)** |
| `.gui` | UTF-8 |

## Thumbnail Generation

The `assets/generate_thumbnail.py` script generates a 512×512 Steam Workshop thumbnail from any input image. It resizes the image, overlays the mod title on two centered lines at the top, and applies white text with a black dropshadow for readability.

Requires [Pillow](https://pillow.readthedocs.io/):
```bash
pip install pillow
python assets/generate_thumbnail.py input_image.png output_thumbnail.png
```

## Credits

This mod was created with the help of AI: **opencode** running the **Kimi K2.6** model.

Inspired by ["No limitations - Megastructures"](https://steamcommunity.com/sharedfiles/filedetails/?id=2611073465) on the Steam Workshop.

## License

This project is provided as-is for the Stellaris modding community. Feel free to fork, adapt, or incorporate into your own mods with attribution.
