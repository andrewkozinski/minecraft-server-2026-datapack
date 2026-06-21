# MyPack — Custom Discs + Structures

Scaffolded for **Minecraft Java 26.2** (the current "latest release", data pack
format 107 / resource pack format 88). If you end up on a different version,
just update the `min_format`/`max_format` numbers in the `pack.mcmeta` files —
see https://minecraft.wiki/w/Pack_format for the right numbers.

## Before anything else: rename the namespace

Every file currently uses the namespace `mypack`. Find-and-replace `mypack`
with your own short, unique namespace (lowercase, no spaces, e.g. `frosthollow`)
across all files and folder names before you start customizing content —
this avoids clashing with other people's datapacks/resourcepacks.

## Folder layout

```
mypack-datapack/
  pack.mcmeta
  data/mypack/
    jukebox_song/              <- defines each custom song (length, redstone output, sound)
    function/
      give/                    <- /function mypack:give/disc_<name> to test-spawn a disc
      load.mcfunction          <- runs once when the pack loads
    loot_table/chests/         <- chest loot for each structure (includes the discs)
    structure/<name>/          <- raw .nbt structure pieces (made with a structure block)
    tags/
      function/load.json       <- hooks load.mcfunction into #minecraft:load
      worldgen/biome/has_structure/  <- which biomes each structure can spawn in
    worldgen/
      structure_set/           <- spacing/frequency of each structure in the world
      structure/                <- the jigsaw structure definition (size, biomes, step)
      template_pool/<name>/     <- pool(s) of pieces a structure picks from

mypack-resourcepack/
  pack.mcmeta
  assets/mypack/
    sounds.json                <- maps sound IDs to .ogg files
    sounds/music_disc/         <- put your .ogg music files here
```

## How the two systems fit together

**Custom discs (new items, vanilla discs untouched):**
Since 1.21, any item can carry a `minecraft:jukebox_playable` component
pointing at a custom `jukebox_song` you define — it doesn't need to be a
"real" disc, and you never edit the vanilla `music_disc_*` loot tables or
recipes, so existing creeper-drop discs are completely unaffected. The
example discs here reuse the `minecraft:music_disc_11` and `_13` item IDs as
a base (so they already render and sound like a disc with zero resource-pack
work) but every copy you hand out always carries your own name, lore, and
song — they're functionally new items. If you'd rather they not share an ID
with any vanilla disc at all, base them on a plain item instead (e.g.
`minecraft:paper`) and add a custom `item_model` component pointing at a
model in your resource pack.

**Structures with disc loot:**
`worldgen/structure_set` controls *where/how often* a structure tries to
generate. `worldgen/structure` (a jigsaw structure) controls *what* gets
built, by picking pieces from a `template_pool`. The actual pieces are
`.nbt` files you make in-game with a structure block — there's a `READ_ME`
placeholder in each `structure/<name>/` folder explaining exactly how to
save one and how to pre-attach the loot table to a chest before saving.

## Testing it

1. Drop the `mypack-datapack` folder into `<world>/datapacks/`.
2. (Optional, for actual audio) also install `mypack-resourcepack` as a
   resource pack — the datapack alone is enough to test that everything
   wires up correctly even before you have real audio, since jukeboxes will
   just play silently / the comparator output and tooltip text still work.
3. Run `/reload`, then `/function mypack:give/disc_frostfall_theme` to get a
   test disc, or `/give @s music_disc_11[jukebox_playable="mypack:frostfall_theme"]` directly.
4. To test structure generation without waiting to explore, use
   `/place structure mypack:frostfall_ruins ~ ~ ~` once you have a real
   `.nbt` file in place (won't work with just the placeholder note).

## Adding a third disc / structure

Copy the pattern of any existing pair of files (jukebox_song +
give-function + loot_table, or structure_set + structure + template_pool +
biome tag) and rename consistently — there's no limit beyond keeping names
unique within the namespace.
