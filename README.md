# Blink Teleport
## For Genshin Impact Miliastra Wonderland

Tired of purple loading screen on long-teleports? Annoyed by 1-sec delay between short-teleports? Frustrated with revival screen after drowning? Want to instantly teleport at any distances, revive in place and get rid of the loading screens for good!?

### Download

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
- `Player - Blink_Teleport`: mount on Player (has node graph vars)
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
- Do not call `Revive Character` between `When the Character is Down` and `When All Player's Characters Are Down` events unless 6 seconds passed; make self-teleport after a revival
- Do not apply Struggle to a downed character, do not long-teleport a downed player
- Do not apply `Struggle` + `Cannot Recover HP` together

## Important note after importing:

Go to `Combat Preset` → `Unit Status` → select `Blink_Teleport_Caster` → `Effect Details` tab → `Character Hidden` effect – make sure the filter is `Blink_Teleport_FALSE`

In the same status, down below, find `Trigger Skill on Timer` effect – make sure its skill chosen is set to `Blink_Teleport_Skill`

Select status `Blink_Teleport_Struggle`, in the same way for `Character Hidden` effect – make sure the filter is `Blink_Teleport_FALSE` again.

This is needed because Miliastra editor tends to mess up their references when importing assets.

## About skill slots 12 and 13:

This library manages two characters skills:
- `Blink_Teleport_Interrupt` assigned to `Slot 12` (contains short animation to interrupt Struggle state gracefully)
- `Blink_Teleport_Skill` assigned to `Slot 13` (contains client-side logic for blinks)

Your stage should not use these slots, otherwise blinks would not work. Since there are 15 custom skill slots available for developers, you should either use other numbers than 12 and 13, or you will have to change their references in Blink Teleport node graphs. Here is where to find them:

- Entity node graph `Player - Blink_Teleport` contains 4 instances of `Add Character Skill` node, referencing slots 12 and 13
- Status node graph `Blink_Teleport_Unstuck` contains `Add Character Skill` and `Cast Skill From Specific Panel Slot` nodes, referencing slot 12
- Client node graph `Blink_Teleport_Skill` at the very end contains `Cast Skill From Specific Slot` node, referencing slot 12
- Status `Blink_Teleport_Caster` has the effect `Trigger Skill on Timer`, referencing Skill Slot 13 in the editor
