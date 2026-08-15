# Blink Teleport
## For Genshin Impact Miliastra Wonderland

Tired of purple loading screen on long-teleports? Annoyed by 1-sec delay between short-teleports? Frustrated with revival screen after drowning? Want to instantly teleport at any distances, revive in place and get rid of the loading screens for good!?

### Download (version 4)

- Stage: [Blink_Teleport_Demo.gil](https://codeberg.org/klee_windtrace/BlinkTeleport/raw/branch/master/Blink_Teleport_Demo.gil)
- Asset: [Blink_Teleport.gia](https://codeberg.org/klee_windtrace/BlinkTeleport/raw/branch/master/Blink_Teleport.gia)
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
- Create `Blink_Teleport_Lib` variable on **Character** of type `3D Vector List` (note that in older versions it was created on Player instead)

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
- Do not apply Struggle to a downed character (**use `Yielding Status List` of all your Statuses with Struggle effect, to put there `Blink_Teleport_Revive` status which is applied to downed characters by the library**)
- Do not long-teleport a downed player
- Make self-teleport after a revival

## About skill slot 13:

This library manages a character skill assigned to `Slot 13` named `Blink_Teleport_Skill`

It does this by briefly binding it to the slot for `Trigger Skill on Timer` effect to work, and unbinding afterwards. This should restore anything you might also have bounded to Slot 13, without interfering.

However, if you want to change the slot number for blinks, do it in the following places:
- Entity node graph `Player - Blink_Teleport` contains `Bind Custom Skill Instance to Specified Slot` node right on the first screen of graph view – select a different slot there
- Status `Blink_Teleport_Caster` has the first effect `Trigger Skill on Timer`, referencing Custom Skill Slot 13 in the editor – select there the same chosen slot

Nothing else references the slot number. Also, the library gracefully detects changes of player Class, reinstantiating its helper skills.

## How to update Blink Teleport from an older version

Before importing the new version, follow these steps:

### Inside the in-game editor
1. Move `Blink_Teleport_Lib` (3D Vector List) variable from Player to Character in all your Templates
2. Delete all 4 related Unit Statuses: `Blink_Teleport_Unstuck`, `Blink_Teleport_Caster`, `Blink_Teleport_Revive`, `Blink_Teleport_Struggle`
3. Delete all 2 related Skills: `Blink_Teleport_Skill`, `Blink_Teleport_Interrupt`
4. Unmount `Player - Blink_Teleport` from all Player templates
5. Remember where you had put the contents of `Character - Blink_Teleport`; it should be just mounted on Character in all templates, but as it is allowed to just copy nodes to your own graph – find and take a note of it, then delete/unmount.
6. Find where `Singleton - Blink_Teleport` is mounted; it should be just on Stage, or your any other global controller-alike entity. Note it and unmount from there.
7. Open Skill Variable Management (further down in menu list) and delete `Blink_Teleport_Cnt` variable.

### Inside the node graph editor 
1. Delete all 3 related Entity Node Graphs (server): `Character - Blink_Teleport`, `Player - Blink_Teleport`, `Singleton - Blink_Teleport`
2. Delete all 3 related Status Node Graphs (server): `Blink_Teleport_Caster`, `Blink_Teleport_Revive`, `Blink_Teleport_Unstuck`
3. Delete the single related Character Skill Node Graph (client): `Blink_Teleport_Skill`
4. Delete the single related Boolean Filter Node Graph (client): `Blink_Teleport_FALSE`
5. Open the Server Signal Explorer and delete `Blink_Teleport_Signal`, apply changes
6. Open the Node Explorer and go to Composite Nodes and locate the folder when the blinks-related nodes are stored for you (it might be named `导入资产` if you never touched it). You need to rename all 7 related composite nodes, prefixing their name (eg. with `_` at the start): `Blink Teleport Player`, `Is Character Down`, `Is Player Blink Teleporting`, `Unstuck Character`, `When Blink Teleport Completes`, `When Blink Teleport Starts`, `When Player Is Loaded`

### Importing the new version
1. Finally, import `Blink_Teleport.gia` normally. It is better to check `Import Assets` folder name target
2. Remove `Blink_Teleport - var on Char` Player Template
3. Mount `Player - Blink_Teleport` on Player in all your Templates
4. Restore `Character - Blink_Teleport` to where it was for you, or just mount on Character in all Player Templates
5. Restore `Singleton - Blink_Teleport` to the controller entity of your choice, or just mount on Stage
6. You may rename `Import Assets` created folder in Unit Statuses and Skills for convenience.

### Replacing composite nodes
1. This is the hardest part; now you will have 7 old blink-related prefixed Composite Nodes, and 7 new imported composite nodes
2. You will need the remove all prefixed nodes that have no references; to check the reference count, open each composite node and try to save it. The popup will show a number of graphs that use this node (each gives +1 even if the node is used several times there)
3. So, if any of 7 prefixed composite nodes have 0 references, delete them
4. For every remaining blink-related prefixed node (especially the main one, `_Blink Teleport Player`), you will have to manually find where you had used them in your stage
5. Then, manually replace each usage with the fresh non-prefixed copy, rewiring all connections
6. As there is no other correct way, I can't recommend doing this differently. But if you're confident in your understanding of the editor, you may use tricks you know (like merging input pins) to simplify this work.
7. At the end, all remaining prefixed nodes would have zero references; now delete them too
