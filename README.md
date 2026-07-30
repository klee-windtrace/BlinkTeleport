# Blink Teleport
## For Genshin Impact Miliastra Wonderland

Tired of purple loading screen on long-teleports? Annoyed by 1-sec delay between short-teleports? Frustrated with revival screen after drowning? Want to instantly teleport at any distances, revive in place and get rid of the loading screens for good!?

### Download (version 3)

- Stage: [Blink_Teleport_Demo.gil](https://codeberg.org/klee_windtrace/BlinkTeleport/raw/branch/master/Blink_Teleport_Demo.gil)
- Assets: [Blink_Teleport.gia](https://codeberg.org/klee_windtrace/BlinkTeleport/raw/branch/master/Blink_Teleport.gia)
- Resource Center page: https://act.hoyoverse.com/ys/prod/ugc/component-store/index.html#/item/2070859182399037440
- Discord discussion thread: https://discord.com/channels/1163728255283642450/1520439768876318820/1520439768876318820

### Features:

- Blink teleport at any distance – quick, client-confirmed, robust, with priority
- Revival in place even after abnormal downing – from which you may blink wherever you need
- Unstuck button – to interrupt infinite falldowns and hanged skill animations by simple status
- Graceful detection of player loading – utilizing the same underlying skill casts as blinks

### Installation
- `Player - Blink_Teleport`: mount on Player (has node graph variables)
- `Character - Blink_Teleport`: mount on Character (or copy nodes)
- `Singleton - Blink_Teleport`: mount on Stage (or any global entity)
- Create `Blink_Teleport_Lib` variable on Player of type `3D Vector List`

### Usage (composite nodes)
- `Blink Teleport Player`: accepts location & rotation, optional integer Priority, and a flag to keep local character's facing direction
- `When Blink Teleport Starts`: event to be listened on Player if needed
- `When Blink Teleport Completes`: its counterpart, provides Priority of a finished blink
- `When Player Is Loaded`: fires once when the client has fully entered the stage
- `Unstuck Character`: applies a status that interrupts skills just like a self-teleport would
- `Is Player Blink Teleporting`: to test for a blink in progress and get its Priority
- `Is Character Down`: to know if a character has not yet revived

### BEWARE OF MILIASTRA BUGS:
- Do not call `Revive Character` between `When the Character is Down` and `When All Player's Characters Are Down` events
- But if 6 seconds passed, do that by force (as this library does)
- Do not apply Struggle to a downed character
- Do not long-teleport a downed player
- Make self-teleport after a revival

## About skill slot 13:

This library manages a character skill assigned to `Slot 13` named `Blink_Teleport_Skill`

It does this by briefly binding it to the slot for `Trigger Skill on Timer` effect to work, and unbinding afterwards. This should restore anything you might also have bounded to Slot 13, without interfering.

However, if you want to change the slot number for blinks, do it in the following places:
- Entity node graph `Player - Blink_Teleport` contains `Bind Custom Skill Instance to Specified Slot` node right on the first screen of graph view – select a different slot there
- Status `Blink_Teleport_Caster` has the first effect `Trigger Skill on Timer`, referencing Custom Skill Slot 13 in the editor – select there the same chosen slot

Nothing else references the slot number. Also, the library gracefully detects changes of player Class, reinstantiating its helper skills.
