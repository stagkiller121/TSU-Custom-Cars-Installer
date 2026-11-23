#RULES FOR USING THIS AND SHARING MODELS ON THE WORKSHOP: 

- MODELS MUST NOT HAVE LOGOS OF MANUFACTURERS
- NO SYMBOLS RELATED TO VIDEO GAMES, OR ANYTHING RELATED TO COPYRIGHT INFRINGEMENT
- IF YOU ARE GOING TO IMPORT FROM ANOTHER AUTHOR, YOU MUST CREDIT THE AUTHOR AND PROVIDE THE LICENSE AND THE LINK TO MODEL AND AUTHOR

# TSU Custom Cars


FOR DAMAGE PHYSICS MOD: https://github.com/stagkiller121/TSU-Crash-Damage

FOR AUTO AI PATHING FOR ALL CUSTOM TRACKS: https://github.com/stagkiller121/TSU-Auto-AI

FOR ORGANIZED PLUG-INS TO SHOW IN GAME: https://github.com/stagkiller121/TSU-Addon-Plugins

A plugin for **Turbo Sliders Unlimited** that brings custom 3D car models into the game. Turbo Sliders ships 18
car bodies; with this plugin a vehicle can use any `.glb` model on top of the stock physics, and custom cars are
shared through the normal **Steam Workshop**.

- **Workshop cars just work**: subscribe to a custom car and it appears in the game right away, with its model and
  paint, without restarting. Unsubscribe and it is gone again.
- **Everywhere in the game**: vehicle list, races, the vehicle editor's **Model** list, **CUSTOMIZE**, lap ghosts
  and saved ghost recordings.
- **Paintable**: the car's paint parts take your paint job from CUSTOMIZE (base colour, metallic, stripes,
  spheres, decals), like a stock body.
- **Fair**: physics, collisions and handling are always those of the stock body the car is built on, so a custom
  car races exactly like a stock vehicle with the same settings.
- **Works for everyone**: players without the plugin see the stock body (with the car's physics), so a shared
  vehicle is never broken.

**Who needs what**

| You want to... | You need |
|---|---|
| Drive custom cars from the Workshop | The installer, once. Then subscribe and play. [For players](#for-players) |
| Make your own custom cars | The installer, a `.glb` model, optionally Blender. [For car makers](#for-car-makers) |

---

## For players

### Requirements

- Turbo Sliders Unlimited on Steam (Windows). Tested with game build 25863877 (2026-11-20).
- The **.NET 8 Desktop Runtime** for the installer: https://dotnet.microsoft.com/download/dotnet/8.0

### Install

1. Close Turbo Sliders.
2. Download `TSU-CC-Release-<version>.zip` from the
   [Releases](../../releases) page and extract it.
3. Run `TSU Custom Cars Installer.exe`. It finds the game through Steam.
4. Click **Install / Repair**. It installs the mod loader (BepInEx 6.0.0-be.789, downloaded and checked for you),
   the plugin and its settings.
5. Start the game. **The first start takes about a minute longer** while BepInEx prepares itself; after that it
   starts normally.

That's it. You never need the installer's **Add car** button; that is for car makers.

**Install by hand instead:** extract BepInEx `BepInEx-Unity.IL2CPP-win-x64-6.0.0-be.789+6cea286.zip` into the game
folder (the folder with `TSU.exe`), then extract `TSU-CC-TSUCustomCars-<version>-manual.zip` into the same folder.

### Play

1. Find a custom car on the Turbo Sliders Steam Workshop and click **Subscribe**.
2. In the game it shows up within a few seconds (no restart): PLAY > VEHICLES.
3. Race, paint it in CUSTOMIZE, race AI, record ghosts - like any other vehicle.
4. **Unsubscribe** on the Workshop and the car leaves the game within a few seconds. Steam deletes the download
   when you close the game.

### Online

Custom models are drawn only in local games and on servers you host as Private or Hidden. On public servers,
and when joining other people's servers, everyone sees stock bodies (joining a friend's server can be allowed
with `AllowClientOnAllowlistedHosts` and `ClientAllowlist` in the config). Unfinalized vehicles cannot be used
online at all - that is the game's own rule.

### Uninstall

- Installer: **Uninstall...** removes the plugin, its settings, every car pack and the plugin's Model-list
  choices; BepInEx only if the installer installed it (it asks). It also finds vehicle files that carry a custom
  model (your own vehicles and Workshop downloads) and offers to remove just those models: the vehicles stay and
  go back to their stock body. Nothing else in your saves is touched.
- By hand: delete `BepInEx\plugins\TSUCustomCars\` and `BepInEx\config\com.stagk.tsucustomcars.cfg`. To remove
  BepInEx too: `winhttp.dll`, `doorstop_config.ini`, `.doorstop_version`, `changelog.txt`, `dotnet\` and
  `BepInEx\` in the game folder.
- Quick off switch: in `BepInEx\config\com.stagk.tsucustomcars.cfg` set `Enabled = false`.

No original game file is changed.

### Troubleshooting

- Log file: `<game>\BepInEx\LogOutput.log` (lines from `TSUCustomCars`).
- A Workshop car shows a stock body: check that the plugin is installed (the log has `TSUCustomCars ... loaded`)
  and `Enabled = true` in the config. The car's author must have shared it with the plugin installed, otherwise
  the upload carries no model.
- After a game update BepInEx rebuilds its files on the first start (one slow start). If the game code changed a
  lot, a plugin update may be needed - check the Releases page.

---

## For car makers

How it works in short: you turn a `.glb` model into a **car pack** with the installer, build a vehicle on it in
the game's vehicle editor, and share that vehicle on the Workshop. **The plugin puts the model inside the shared
vehicle file**, so subscribers get it automatically; they never need your pack.

### What a model needs

- **glTF binary (`.glb`)**, Y up, the car facing forward along +Z (the glTF default).
- **Four separate wheels** (`wheel_FL`, `wheel_FR`, `wheel_RL`, `wheel_RR`). Merged wheels are split by the prep
  script below.
- A sensible size: around **30k triangles** works well; the prep script decimates bigger models.
- **Only use models you are allowed to share**, and keep their credits (`CREDITS.txt`) with the pack.

### 1. Prepare the model (Blender 4+/5)

Sketchfab and similar models usually need this (merged wheels, interiors, 200k+ triangles):

```
blender -b -P tools/prep_car_glb.py -- <in.glb> <out folder> <name> <prep.json>
```

`prep.json` lists which materials to delete (e.g. the interior) and which are wheels, glass and paint - see
`tools/cars/clk_gtr.prep.json` for an example. The script splits the wheels into `wheel_FL/FR/RL/RR`, decimates to
about 28k triangles, turns the car the right way round, writes preview images, and unwraps the **paint**
materials into a clean UV layout (paint jobs need UVs inside 0..1 that do not overlap).

### 2. Make the car pack

In the installer: **Add car...** > pick the `.glb` > enter a **Name**, Body **Auto** (picks the stock body whose
wheelbase is closest), Mode **add**. The pack goes to `<game>\BepInEx\plugins\TSUCustomCars\cars\<name>\` and adds
a new vehicle with that name to the game.

You can also write the pack's `.json` by hand like `tools/cars/clk_gtr.json`; packs can be shared as a zip with
the `.json`, the `.glb` and `CREDITS.txt`, and added with **Add car...**.

### 3. Tune it

Edit the pack's `.json` and respawn the car (restart button in the vehicle editor) - changes show without
restarting the game. Useful keys:

| Key | What it does |
|---|---|
| `wheels.inset` | Push wheels into the body (metres, + = inward), per `front`/`rear` or `FL/FR/RL/RR` |
| `wheels.modelTrack` | Put the wheels at the model's own track width |
| `transform.offset` / `transform.scale` / `transform.fitToWheelbase` | Position and size of the body |
| `vehicle.name`, `vehicle.description` | What the vehicle list shows (description 4-128 characters) |
| `materials.paint` | Materials that take the player's paint job, e.g. `["body"]` |

### 4. Build your vehicle in the game

1. CREATE > VEHICLE EDITOR > the car icon (top right) > **Model** > pick your custom model (it is listed after
   Hypercar; also in the CUSTOMIZE model stepper).
2. Tune the physics as usual, paint it in CUSTOMIZE, save, and **finalize** it.
3. **AI opponents** need AI lines for that exact vehicle on each track: CREATE > AI EDITOR (only finalized vehicles
   are listed). Hotlapping does not record AI lines.

### 5. Share it on the Workshop

Share the finalized vehicle from the vehicle editor as usual. The plugin adds the model to the upload; the log
shows `embedded model '<id>' (... KB) into <vehicle>.veh ... (Workshop share)`. Expect the Workshop item to be a
few MB (the model) instead of a few hundred bytes. Put the model's credits and licence in the Workshop
description.

**Testing your upload:** on your own PC your local copy of the vehicle always wins over the Workshop download
(it is the same vehicle), so a subscriber's view is best checked from a second PC or Steam account with only the
plugin installed.

---

## Building from source

- Plugin: `dotnet build src/TSUCustomCars -c Release -p:UseInterop=true` (needs BepInEx installed in the game,
  path via `-p:TSUGameDir=...`).
- BepInEx interop files without starting the game:
  `dotnet run -c Release --project tools/OfflineInterop -- "<game dir>"`.
- Tests: `dotnet run -c Release --project tests/TSUCustomCars.Tests` (offline, against stub data; close the game
  first).
- Installer: `dotnet publish src/TSUCustomCars.Installer -c Release -o out` (it bundles the plugin from
  `src/TSUCustomCars/bin/Release`).
- Release zip: `python -I tools/make_release.py <version> <plugin dll> <installer exe> <out dir>`.

## Credits

- [BepInEx](https://github.com/BepInEx/BepInEx), [Il2CppInterop](https://github.com/BepInEx/Il2CppInterop),
  [Harmony](https://github.com/pardeike/Harmony), Cpp2IL - the modding framework.
- Turbo Sliders Unlimited belongs to its developers. This is an unofficial fan mod, not affiliated with or
  endorsed by them.
- Example car (CLK GTR pack): model "Mercedes-Benz CLK GTR" by Dave Bored, CC-BY-4.0 (see the pack's
  `CREDITS.txt`). Mercedes-Benz and CLK GTR are trademarks of Mercedes-Benz Group AG; not affiliated or endorsed.
- **AI disclosure**: the plugin, installer and tools were written with Claude (Anthropic, Claude Code, model
  Claude Opus 5.5), directed and play-tested by the author.

## License

The code in this repository is MIT licensed (see `LICENSE`). Car models keep their own licences (see each pack's
`CREDITS.txt`).
