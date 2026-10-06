# In-game feature audit â€” 6 October 2026

**Status: incomplete.** Checked entries mean the stated action or appearance was observed through the running game UI. They do not certify the entire feature. Source inspection, automated unit results, catalogue presence, and setup commands alone are not gameplay verification.

## Visuals, inventory, and interaction

- [x] Desktop and mobile-sized world entry completed.
- [x] E inventory: creative catalogue, slot layout, hover item names, and player preview visible.
- [x] Catalogue exposed 13 mob spawn eggs; `/give @s cow_spawn_egg` added one.
- [x] Cow model and corrected skin visibly inspected, including a stationary test mob.
- [x] Held sword rotation and framing visibly inspected after repair.
- [x] Bed placement produced both halves; sleeping overlay appeared.
- [x] Native bed end face corrected; subsequent side view showed wood below mattress.
- [ ] Latest bed side horizontal flip: pillow side patch must align with pillow on top, in both packs.
- [x] Chest screen repaired after catalogue overlay bug; bucket storage transfer observed.
- [x] Door opened by interacting with upper half.
- [ ] Chest opening/closing animation and every chest variant.
- [ ] Every block/item model, texture, orientation, animation, and outline.
- [ ] Inventory splitting, dragging outside, Q dropping, equipment, and save/reload persistence.

## Movement, survival, and combat

- [x] Elytra gliding moved without WASD input.
- [x] Rocket use consumed an item and accelerated flight upward: HUD Y221â†’232, Z730â†’759.
- [ ] Stair/slab stepping, swimming exit, collisions, sprinting, and sneak edges.
- [ ] Mining, placing, item drops/pickup, sound, and frame latency.
- [ ] Bow charge/release while moving camera; arrows and skeleton shooting.
- [ ] Melee recharge, critical hits, armor, knockback, mob fleeing, and fireball deflection.
- [ ] Hunger/exhaustion/regeneration, suffocation, cactus/lava/fire damage, and respawning.
- [ ] Lava destruction of drops except netherite; beds exploding in Nether/End; TNT fuse visibility.

## Fluids, lighting, and terrain

- [x] Sealed-room darkness and torch placement/removal visibly observed.
- [x] Lava animation inspected in both packs.
- [x] Single-source lava flow appeared continuous after side-face repair.
- [ ] Water range, downward flow, source creation/removal, fountains, and underwater internal edges.
- [ ] Nether water restriction.
- [ ] Lighting across chunk borders; sky exclusion underground; weather/night transitions and stateful emitters.
- [ ] Predictive chunk loading during fast travel, edits at borders, and high render distances.
- [ ] Flower/grass distribution, foliage opacity/tints, growth, dripleaf, and spore-blossom interaction.

## Machines and storage

- [x] Chest opening UI and bucket transfer.
- [ ] Crafting 2Ã—2/3Ã—3: ingredient consumption and output.
- [x] Repaired furnace UI accepted iron ore and fuel; fuel consumption and flame/progress appeared.
- [ ] Furnace output completion and collection after latest progress-DOM repair; blast furnace/smoker, sounds, and persistence.
- [ ] Hopper pickup/transfer/locking; dispenser/dropper behavior.
- [ ] Brewing, enchanting, anvil, smithing, stonecutter, crafter, villager trade, and jukebox.
- [ ] Loot tables and structures inspected in naturally generated worlds.

## Redstone and vehicles

- [x] Lever model changed immediately and powered dust changed visibly.
- [ ] Complete leverâ†’dustâ†’lamp on/off response and every lever mounting direction.
- [ ] Piston push/retract/sticky pull, limits, and texture states.
- [ ] Redstone torch inversion/burnout, repeater delay, comparator, observer, and daylight detector.
- [ ] Buttons/plates, tripwire, sensors, target, bulb, trapdoors, iron doors, and container output.
- [ ] Rails: straight/corner/slope geometry; cart riding, ramps, powered braking/acceleration, pushing, exiting, and destruction.

## Dimensions and End fight

- [x] End arrival, loading, and boss bar appeared.
- [x] Punching a crystal removed it.
- [x] Repaired dragon `/kill` generated exit portal.
- [x] End exit showed loading and returned to Overworld surface.
- [ ] Nether portal normal/dynamic traversal in both directions, close-range views, multiple portals, entity visibility/travel, and safe Nether arrival.
- [ ] Dragon complete flight/perch/attack cycle, multipart damage, healing, crystal explosion, natural death, and rewards.
- [ ] End gateway generation/traversal and return.

## Commands and chat

- [x] `/summon cow ~ ~ ~-3` produced a visible model and local response.
- [x] `/give`, `/tp` coordinates, `/tp` yaw/pitch, `/fill ... air`, and dragon `/kill` executed through chat.
- [ ] Remaining commands, relative coordinates, entity selectors, player teleportation, errors, and permissions.
- [ ] Chat privacy/broadcast behavior and multiplayer commands.

## Multiplayer and mobile

- [x] Mobile landscape HUD showed joystick/action buttons without overlap; menu scrolled to multiplayer controls.
- [ ] Actual touch movement, looking, mining, using, inventory drag/drop, bow, and fullscreen on a phone.
- [ ] Host/join across devices/networks; player model identity, collisions, combat, entity/crystal/block/inventory/dimension synchronization, disconnect/rejoin, and relay behavior.

## Evidence and testing limitations

Inventory/mobile evidence: `audit-gameplay-inventory.png`, `audit-mobile-game.png`, `audit-mobile-initial.png`.
Resumed root gameplay evidence: `audit-chest-fixed.jpg`, `audit-bed-fixed.jpg`, `audit-bed-side-fixed.jpg`, `audit-lava-fixed.jpg`.

Earlier concurrent browsers and shell diagnostics stalled. Those stalls were not attributed to a gameplay bug. The resumed audit uses one responsive IAB game at a time. Latest bed horizontal alignment remains pending; earlier asset comparisons do not count as in-game confirmation.

## Machine audit candidates from implementation inspection — NOT in-game confirmations

These findings guide the next UI tests; they must not be labeled gameplay-verified until reproduced.

- Smithing restrictions and incorrect target durability were patched using the shared equipment registry. In-game axe/armor upgrade and preserved-damage confirmation remain pending.
- Stonecutter now uses 254 native recipe records filtered to registered input/output items, with selectable results. In-game stone/cobblestone/sandstone selection and counts remain pending.
- Anvil repair now uses shared equipment repair metadata and capped quarter-durability restoration; rename leaves unrelated material untouched. Actual repair/rename/result collection remain pending.
- Enchanting screen excludes bows/books and other enchantable equipment; offers three fixed enchantment categories.
- Brewing screen supports only six base potion conversions. Test three-bottle batches, fuel persistence, closing/reopening during brewing, and collecting results.
- Villager trade interaction currently passes through the base trade interface rather than the machine trade-screen renderer; verify its actual layout separately.

Use these negative cases alongside happy paths: diamond axe + netherite template + ingot; diamond helmet + diamond repair; stone in stonecutter; bow/book in enchanting table.

## Resumed UI audit — 7 October 2026
- Trapped chest and shulker: opened explicit fixtures, inspected native slot alignment, deposited/withdrew Stone bricks.
- Anvil rename preview immediately displays output; collected renamed Netherite sword.
- Smithing: diamond axe + template + netherite ingot produced Netherite axe; all ingredients consumed and output collected.
- Entity selector /kill @e[type=cow] removed only cows; cow beef/leather sprites seen on ground. Direct sheep hit produced wool + raw mutton; both collected and named correctly in hotbar.
- Inventory right-click split 62 rockets into 31/31. Q reduced cursor by one but picked it back up almost immediately; pickup delay repair in progress.
- Standing on cactus top caused no damage before repair. Expanded contact boundary, rebuilt, reentered exact fixture: health fell from 17 to 5 and then death returned to Overworld surface. Post-death browser rendering slowed; no console errors, cause not yet established.
- /weather clear visibly removed rain; /tp yaw/pitch and /gamemode exercised during fixtures.
Full audit remains in progress; these are bounded observations, not blanket feature certification.

- Latest cyan bed side alignment verified in BOTH Minecraft and Bare Bones packs: pillow at head, wood and legs below mattress. One held bed placed both halves; texture toggle reloaded and preserved world/bed.
- Q hotbar drop immediately reduced bed stack 2→1 after pickup-delay patch; delayed recollection still being checked.

- Pickup-delay retest: Q reduced bed stack 2→1 initially, then after delay it returned to 2 by pickup. Normal mining delay has separate subsystem tests; exact timing on phone/network not gameplay-verified.
- Lever→dust→lamp lit; removing lever turned dust/lamp off; restored fixture. Held bed blocked lever use until empty hand selected (possible interaction priority issue; not patched yet).
- Normal piston pushed stone one block and retracted without pull. Sticky piston pushed and pulled stone back; extension/rod models visibly present.
- Redstone torch on stone lit adjacent lamp; powering its support extinguished torch and lamp. Removing input restored circuit.
- Elevated water source flowed down and spread across floor; source removal recession check in progress.
- Held-bed lever regression fixed and retested: right-click changed the handle in both directions while Cyan bed remained selected.
- Water source removal completed: elevated flow receded and left dry ground.
- Render slider reached 32 chunks with the >16 warning; returning to 5 hid the warning.
- Dynamic Nether portal rendered destination at close range and traversal changed HUD to Soul Sand Valley; return crossing changed HUD back to Plains. Screenshots alone do not certify absence of a single-frame flash.
- Nether bed use removed the bed and blasted surrounding terrain. Water bucket produced no water; Creative extra empty bucket reproduced and patched, UI retake pending.
- Sealed room spanning X=15/16: after updates, daylight and midnight both read light 0. Torch at X=15 lit player at X=16 to 11; removal returned light 0 and visibly darkened the room.
- Two actual browser players joined (Alice/Bob). Both /list responses named both players. Both /say messages appeared on both clients. Guest /tp Alice Bob changed the host position correctly.
- Host placed Glowstone; guest saw it. Guest left-click removed it; host also saw it gone. Remote Alice model, name tag, and held bucket were visible on guest.
- Guest /tell Alice reproduced Server: Player not found. Host-recipient routing patched; fresh-session UI retake pending. These same-machine tests do not prove cross-network relay connectivity.
- Private whisper repair passed in a fresh two-player session: Bob to Alice appeared on host without the Server error; Alice reply reached Bob.
- Guest punch removed half a heart from Survival host and visibly knocked the host back. Creative guest remained in Creative.
- Creative water-bucket retake: water appeared in Overworld and existing empty-bucket count remained 1, without adding another bucket.
- Flint and steel ignited a TNT fixture: primed TNT remained visible before explosion, then a crater appeared. Flint and steel also placed visible fire on grass.
- Audit stopped at the user's release request. Remaining unchecked feature combinations are not certified.
