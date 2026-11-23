# Batman: Arkham Trilogy — native iPhone port

## Project scope

Build the Nintendo Switch versions of the three games for iPhone through this repository's nxnative pipeline. This is a game-specific native runtime profile and compatibility effort, not a new game engine or a replacement app.

Target device for the first device validation: **iPhone 12 Pro Max (A14, 6 GB RAM)**. This device is in the fork's `--6gb` group and is explicitly marked untested in the current README. The fork exists to address pre-A17 GPUs that cannot sample the Switch's BC-compressed textures.

## Agreed order

1. **Batman: Arkham Asylum** — first bring-up target.
2. **Batman: Arkham City** — second.
3. **Batman: Arkham Knight** — third; its existing upstream profile is a reference, not proof of iPhone compatibility.

Keep this work on `feature/batman-arkham-asylum-port`, separate from other game ports and the iBuilder Unreal work.

## Known starting facts

- Arkham Asylum Switch title ID: `0100E870163CA000`.
- Arkham Knight Switch title ID: `0100ACD0163D0000`.
- The upstream nxnative repository already has an Arkham Knight 1.0.5 profile. Its notes report a successful Mac route to Pauli's Diner at 30 fps, but explicitly say no IPA was built and iOS was not tested.
- Arkham Knight's upstream profile uses Unreal Engine 3, Wwise, CRI Sofdec movies, and Scaleform; it has a 2 GiB guest block and about 3.7 GB process residency in the measured Mac route. Do not copy its memory or shader settings blindly to Asylum or City.
- The Arkham Trilogy publisher confirms the three games are installed as separate library entries. Asylum is on the physical cartridge; City and Knight require downloads for the physical edition.

## First milestone: Asylum profile discovery

Do not add guessed `game.tsv` settings or mark the game supported yet. Use the user's own Asylum base game, update and DLC files, plus their own `prod.keys`, to determine:

- base title ID, update version/build ID and SDK version;
- executable modules and imported modules;
- memory layout, guest heap and system resource requirements;
- graphics API and shader cache format;
- texture formats and the fork's BC-texture fallback behavior;
- audio, movie and filesystem requirements;
- required game-specific rewriter patches, if any.

Then create a minimal profile from observed metadata, run the Mac build/boot route first, and only after that attempt an IPA for the iPhone 12 Pro Max.

## Device compatibility gates

- Start with the fork's documented `--6gb` configuration for the iPhone 12 Pro Max.
- If the app is killed while loading, test the documented `--older-iphone 256` texture cap rather than silently changing unrelated settings.
- The app must be signed with the increased-memory entitlement enabled; record the actual resident-memory/headroom log.
- A successful CI run or Mac boot is not evidence that the game runs on the iPhone. Record each platform separately.

## Not yet verified

- Arkham Asylum or Arkham City profile in this fork.
- Any Batman game running as an IPA on an iPhone.
- Arkham Knight running on an A14 device.
- Full trilogy compatibility, DLC compatibility, saves, controller and touch behavior.

## References

- [Nintendo: Batman: Arkham Asylum](https://www.nintendo.com/us/store/products/batman-arkham-asylum-switch/)
- [Nintendo: Batman: Arkham City](https://www.nintendo.com/us/store/products/batman-arkham-city-switch/)
- [WB Games: Arkham Trilogy FAQ](https://dcgamessupport.wbgames.com/hc/en-us/articles/17942184713747-Batman-Arkham-Trilogy-FAQ)
- [WB Games: storage requirements](https://dcgamessupport.wbgames.com/hc/en-us/articles/23534765560851-Batman-Arkham-Trilogy-Storage-Requirements-on-Nintendo-Switch)
- [Upstream Arkham Knight profile notes](https://github.com/seedds/nxnative/tree/main/dm/games/0100ACD0163D0000)
