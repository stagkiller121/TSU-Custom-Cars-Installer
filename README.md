# TSU Custom Cars

A plugin for **Turbo Sliders Unlimited** that adds custom 3D car models to the game. Turbo Sliders ships 18 car
bodies; with this plugin a car can use any `.glb` model (Blender, Sketchfab, ...) on top of the stock physics.

- **New cars**: a model pack adds a new selectable car (e.g. "CLK GTR") to the vehicle list.
- **Vehicle editor**: add-on models appear in the editor's **Model** list (after Hypercar) and in the
  **CUSTOMIZE** model stepper.
- **Shareable on the Steam Workshop**: when you save a vehicle that uses a custom model, the model is stored
  inside the vehicle's `.veh` file. Players who have this plugin and subscribe to your vehicle see the model
  automatically. Players without it see the stock body (with your physics), so the vehicle works for everyone.
- Lap ghosts and saved ghost recordings show the custom model too and fade like normal ghosts.
- Wheels spin and steer with the stock suspension; the visual wheel width can be tuned per car.

Physics, collisions and handling always stay those of the stock body the car is built on, so a custom car
races exactly like a stock-bodied vehicle with the same settings.

## Requirements

- Turbo Sliders Unlimited (Steam, Windows). Tested with game build 25863877 (2026-11-20).
- **BepInEx 6.0.0-be.789** (Unity IL2CPP, win x64). The installer downloads and checks it for you.
- For the installer: the **.NET 8 Desktop Runtime** (https://dotnet.microsoft.com/download/dotnet/8.0).

## Install

**With the installer (recommended)**
1. Close Turbo Sliders.
2. Run `TSU Custom Cars Installer.exe`. It finds the game through Steam.
3. Click **Install**. It installs BepInEx (if needed), the plugin and a default config.
4. Start the game once. The first start takes about a minute longer: BepInEx prepares itself.

**By hand**
1. Extract BepInEx `BepInEx-Unity.IL2CPP-win-x64-6.0.0-be.789+6cea286.zip` into the game folder
   (the folder with `TSU.exe`).
2. Extract `TSUCustomCars-<version>-manual.zip` into the same folder.

**Add a car pack**: in the installer click **Add car** and pick a pack `.zip`, or copy the pack folder to
`<game>\BepInEx\plugins\TSUCustomCars\cars\<pack>\`.

## Uninstall

- Installer: **Uninstall** (removes exactly what it installed; BepInEx only if it installed it).
- By hand: delete `BepInEx\plugins\TSUCustomCars\` and `BepInEx\config\com.stagk.tsucustomcars.cfg`.
  To remove BepInEx too: `winhttp.dll`, `doorstop_config.ini`, `.doorstop_version`, `changelog.txt`,
  `dotnet\` and `BepInEx\` in the game folder.
- Quick off switch: in `BepInEx\config\com.stagk.tsucustomcars.cfg` set `Enabled = false`.

No original game file is changed. The plugin never writes to your save folder, except one thing: it adds the
model to your own vehicle files that use a custom model (extra entries inside the `.veh` zip; the game ignores
them).

## Using custom cars

- **Race with the added car**: PLAY > VEHICLES > pick e.g. "CLK GTR".
- **Make your own vehicle with a custom model**: CREATE > VEHICLE EDITOR > car icon (top right) > **Model** >
  pick the custom model. Tune physics as usual, save, finalize.
- **AI opponents** need AI lines for that exact vehicle on that track: CREATE > AI EDITOR (only finalized
  vehicles are listed). Hotlapping does not record AI lines.
- **Share on the Workshop**: finalize and share the vehicle from the vehicle editor as usual. The model goes
  with it. Put the model's credits in the description.

### Online

Custom models are drawn only in local games and on servers you host as Private or Hidden. On public servers,
and when joining other people's servers, everyone sees stock bodies. (Joining a friend's server can be allowed
with `AllowClientOnAllowlistedHosts` and `ClientAllowlist` in the config.) Unfinalized vehicles cannot be used
online at all.

## Add your own model

A car model needs four separate wheel parts and must face forward along +Z (glTF default, Y up).

1. **Prepare the model** (Sketchfab models usually need this: merged wheels, interiors, 200k+ triangles).
   With Blender 4+/5:
   ```
   blender -b -P tools/prep_car_glb.py -- <in.glb> <out folder> <name> <prep.json>
   ```
   `prep.json` lists which materials to delete (interior), which are wheels, glass and paint; see
   `tools/cars/clk_gtr.prep.json`. The script splits the wheels into `wheel_FL/FR/RL/RR`, decimates to about
   28k triangles, faces the car the right way and writes previews.
2. **Make the pack**: in the installer, **Add car** > pick the `.glb` > Name, Body **Auto** (picks the stock
   body whose wheelbase is closest), Mode **add**. Or write the `.json` by hand like `tools/cars/clk_gtr.json`.
3. **Tune** in game: edit the pack's `.json` and respawn the car (restart button in the editor) - changes show
   without restarting the game. Useful keys:
   - `wheels.inset`: push wheels into the body (metres, + = inward), per `front`/`rear` or `FL/FR/RL/RR`;
   - `wheels.modelTrack`: put the wheels at the model's own track width;
   - `transform.offset` / `transform.scale` / `transform.fitToWheelbase`;
   - `vehicle.name` and `vehicle.description` (4-128 characters): what the vehicle list shows for the added car.

Only share models you are allowed to share (check the license) and keep their credits with the pack.

## Troubleshooting

- Log: `<game>\BepInEx\LogOutput.log` (lines from `TSUCustomCars`).
- The car shows a stock body: check the log for `rejected` / `error` lines about the pack, and that
  `Enabled = true` in the config.
- After a game update BepInEx rebuilds its files on the first start (one slow start). If the game code changed
  a lot, a plugin update may be needed.

## Building from source

- Plugin: `dotnet build src/TSUCustomCars -c Release -p:UseInterop=true` (needs BepInEx installed in the game,
  path via `-p:TSUGameDir=...`).
- BepInEx interop files without starting the game: `dotnet run -c Release --project tools/OfflineInterop -- "<game dir>"`.
- Tests: `dotnet run -c Release --project tests/TSUCustomCars.Tests` (offline, against stub data).
- Installer: `dotnet publish src/TSUCustomCars.Installer -c Release -o out`.

## Credits

- [BepInEx](https://github.com/BepInEx/BepInEx), [Il2CppInterop](https://github.com/BepInEx/Il2CppInterop),
  [Harmony](https://github.com/pardeike/Harmony), Cpp2IL - the modding framework.
- Turbo Sliders Unlimited belongs to its developers. This is an unofficial fan mod, not affiliated with or
  endorsed by them.
- CLK GTR pack: model "Mercedes-Benz CLK GTR" by Dave Bored, CC-BY-4.0 (see the pack's `CREDITS.txt`).
  Mercedes-Benz and CLK GTR are trademarks of Mercedes-Benz Group AG; not affiliated or endorsed.
- **AI disclosure**: the plugin, installer and tools were written with Claude (Anthropic, Claude Code, model
  Claude Opus 5.5), directed and play-tested by the author.

## License

The code in this repository is MIT licensed (see `LICENSE`). Car models keep their own licences (see each
pack's `CREDITS.txt`).
