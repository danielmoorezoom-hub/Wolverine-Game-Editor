[README.txt](https://github.com/user-attachments/files/33290374/README.txt)
# Wolverine-Game-Editor
Literally thousands of options
Wolverine Tweaks  (Marvel's Wolverine, build i38, PS5 dump on Kyty)

Run WolverineTweaks.exe (needs .NET Framework 4, which Windows 10/11 already has). It finds the game
folder C:\wolverine\i38\Wolverine\Wolverine\PPSA03671-app0 by itself; "Game folder..." picks another.
Close the game before applying anything, then restart it to see the change.

CONTROLS tab
  Every button binding compiled into eboot.bin: 89 binding sets, 284 controller and 127 keyboard bindings.
  "Show" defaults to the developer / debug sets. Pick a new button in the "Button / key" column (or the
  "+ second button" column where there is one), then "Apply changes".
  - 168 controller bindings can be changed. The rest are locked, and the Status column says why:
    usually the value is shared with other places in the program (mostly stick axes), so changing it
    would change those too.
  - Keyboard bindings are shown but not editable. They come from the PC development builds; Kyty gives
    the game a controller, so they can probably not be used.
  - A "second button" means both buttons are held together. That reading comes from Toggle Camera, which
    is L3 + R3; it is untested in the game.
  - The first time you apply, a full copy of the untouched file is kept as eboot.bin.before-wolverine-tweaks.
    "Restore all original bindings" puts back the original byte of every changed binding.
  - Changing a gameplay binding here may fight the game's own Controls menu, which remaps the hero's
    actions at runtime. The debug and developer sets are the ones this is meant for.

GAME TWEAKS tab
  One-byte values in the config archives (d\0\config and d\1\config): the photo mode camera ranges and
  speeds, and the wall-run and wall-stick limits in all three traversal configs. 255 is the most a one-byte
  value holds. "Restore game's values" puts back the shipped numbers.

  Code patches (top of the same tab): switches that change a few bytes of game code in eboot.bin.
  - Photo mode camera passes through walls: photo mode tests each new camera position with ray casts and
    puts the camera back when one hits; this makes it ignore the hits. Tick it and press "Apply patches";
    untick and apply to put the original bytes back.
  - Missions can't lock jumping or sprinting: mission scripts, restrict-moves areas and setup volumes all
    switch hero moves off through one ability-lock function in eboot.bin. This makes it ignore lock
    requests for Jump and Sprint only; every other lock (attacks, climbing, etc.) still works.
  - No forced walking areas: 228 movement areas in the levels force walk only (55), walk or jog (159)
    or no sprint (14). This sets each to the default. Written in place in the zone archives.
  Scripted walks inside cutscenes or animations, and slow "walk style" animations, are separate and
  are not changed. Lifting a lock can also let you jump or sprint out of an area a mission expects you
  to stay in, so untick the patch if a mission gets stuck.
  - No mission bounds: going past the edge of a mission area normally shows "leaving mission area",
    fades the screen and puts you back. This switches off the force-return volumes that do it, so you
    can roam freely; the warning and the grey out-of-bounds effect go too. 2 bytes of eboot.bin.
    Some of these volumes also catch falls out of the map: if you drop through the world, reload a
    checkpoint. Missions that abandon themselves when you go far away (open-world side activities)
    are a separate rule and still do that.
  - Moon jump: hold jump to keep rising, let go to fall. The normal jump and the sprint jump launch
    at moon strength (16 m / 1.6 s), and while the button is held the game's own hold-for-height
    jump thrust (ButtonThrust settings) keeps pushing up with no time limit. The standard jumps don't
    carry ButtonThrust settings, so a few of their air-drag / turn-to-input settings are re-keyed as
    ButtonThrust (air steering may feel slightly different). Fall damage and the heavy-landing
    stagger are moved out to 254 m. Changes the 3 hero traversal configs (d\0\config, d\1\config).
    Restart the game after switching it.
  - No hair strands: strand hair (the detailed hair the game uses on main characters) is skipped
    in the per-frame strand update, the same path as the game's own "skip rendering" strand setting,
    so it isn't drawn and casts no shadow. Hair made of ordinary polygon cards is not affected.
    2 bytes of eboot.bin.
  - Lighting mode (drop-down under the patch list): Game decides / Always day (kDay) / Always night
    (kNight). The game has these two lighting modes. This sets its built-in lighting override in
    eboot.bin (normally -1 = off), so every level loads in the mode you pick and missions can't
    switch it. Areas built for only one mode may look wrong in the other. Set it back to
    "Game decides" to undo. Applied with the Apply patches button.

RENDERING tab
  Ticked = switched off. Each item adds engine-setting lines to commandline.txt; the setting names
  were read from the settings tables in eboot.bin (Engine, Render, Decal, Cloth, Anim, Portal,
  Destructible, RayTracingSettings, Shared, Game, GameUI, TrafficGame, DamageFeedback). A feature
  that is off is never set up, so its memory isn't allocated. Untick and apply to bring it back;
  commandline.txt is backed up next to itself on every save. Restart the game after applying.
  Items: particle/visual effects, decals, GPU particle collision, effects from breaking objects,
  bloom, motion blur, film grain, temporal AA, aspect-ratio blur bars, dynamic light grids,
  ambient shadows, skin subsurface scattering, rain on the lens, water simulation/ripples/foam,
  cloth simulation, Ziva muscle deformation, wrinkle maps, wounds on Logan, portal views,
  ray-traced objects, critters, traffic vehicles, in-world streamed videos, hero tracking light, HUD.
  "Tick all" / "Untick all" buttons. Not included on purpose: the whole renderer (black screen),
  texture streaming (turning it off loads every texture at full size = more memory), menus/UI.

  Model detail (LOD): Game default, LOD 0 ... LOD 5. Puts the game on its performance-mode spec
  (kPS5_60X, two commandline.txt lines) and sets that spec's GeometryLodScaleMultiplier in
  d\0\config: LOD 0 = 1.0 (full distances), then 0.5, 0.25, 0.125, 0.0625, 0.03125, so every step
  halves the distance at which models drop to their lower-detail versions. "Game default" removes
  the two lines and puts back 0.75.

SHADERS (d\1) tab
  wolverine_shaders.exe patches only d\0\materialgraph; the 416 materialgraphs in
  d\1\materialgraph (it says "wrong archive!" for them) keep drawing. This tab turns those off:
  ticked = off. Its toc entry is pointed at an empty materialgraph (the same 16 bytes as
  shaders\dummy) appended once to the end of d\1\materialgraph, so whatever uses it draws
  nothing. The original data stays in the archive, so unticking puts the original toc entry back.
  The toc is backed up once as toc.before-wolverine-tweaks.
  "Tick screen effects" ticks 49 full-screen / post-composite effects: vignette and screen
  transitions, chromatic aberration, heat and other distortions, senses/intuition highlights,
  blind-Logan post process, burning and kill-flash screen overlays, screen blood spatter,
  dissolve/incinerate/cloak post composites, portal wipes and the like.
  "Tick all marked dummy in shader_table.txt" ticks every d\1 graph your table sets to dummy.
  If you run wolverine_shaders.exe again afterwards and it rewrites the toc, reopen this tab:
  entries it changed show as "(?)" and are left alone.

COMMANDLINE.TXT WORD LIMIT
  The game reads commandline.txt into a fixed table of 128 words and crashes at start-up
  (runtimeLinker "Unhandled host exception", rsi=deadbeef...) if there are more. Each old-style
  "{ Name=value }" line costs 3 words, so the tool now keeps every setting in ONE block:
      {
        Render.MotionBlurEnable=false
        ...
      }
  which costs 1 word per setting plus 2. The tool converts the file to this layout the next time
  it saves, shows the word count on the Command line tab, and refuses to save more than 120 words.

ALL SETTINGS tab
  Every setting the game's settings tables in eboot.bin describe: 1883 of them across 111
  categories (Engine, Render, Hero, Bot, Physics, Sound, LoadSystem, Shared, ...), including the
  settings inside nested groups such as Hero.TraversalSettings.* or TrafficGame.PedestrianLoader.*.
  Each row shows the type, the value in commandline.txt (if any) and the game's description where
  it has one. Search filters by name and description (several words = all must match); "Only
  settings in commandline.txt or changed" narrows the list. Pick a row, choose or type a value
  (true/false and named values come as a drop-down; numbers are range-checked) and press Set or
  Enter, then Apply changes. "Game default" removes the setting's line. Changed rows are blue,
  rows already in commandline.txt are green. Many of these are developer, debug or start-up
  settings and some will do nothing in a release build or can stop the game starting: change a
  few at a time. commandline.txt is backed up next to itself on every save.

COMMAND LINE tab
  Ticks for the commandline.txt lines used so far (debug menu, ImGui, press preview, install check,
  pedestrians, debug messages). Other lines are kept. The previous file is copied next to it on each save.

Safety
  Before writing any byte, the tool checks that the bytes around it are exactly what they were in the build
  it was made for, and refuses otherwise. Every change is listed in WolverineTweaks-changes.log next to the
  exe. Errors go to WolverineTweaks-error.txt.

What was tested
  Run under Mono (not Windows) against copies of your eboot.bin, config archives and commandline.txt: all
  411 bindings and 17 settings were found where expected. Changing Toggle Camera's first button to Touchpad
  changed exactly one byte of eboot.bin, and decoding the patched file separately showed Touchpad + R3.
  Not tested: whether the game and Kyty run with a patched eboot.bin, and what each debug binding does in game.

source\ has the C# code, the data generator (gen_data.py with bindings.json / tweaks.json) and, in
decoder\, the Python scripts that read the bindings out of eboot.bin. build.sh builds against the
.NET Framework 4.5 reference assemblies.
