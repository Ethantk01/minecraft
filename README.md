# Voxel Frontier

Version 0.16.21. Includes the modular engine and shared actor systems from 0.15.0, plus resource packs, first-person held items and drawing bows, animated chests, safer spawning and dimension respawning, suffocation, End population and portal rendering repairs.

## Predictive chunk streaming

Version 0.16.21 prioritizes generation and meshing from actual movement, prefetches a six-second corridor (including strafing/backwards movement), and increases bounded loading budgets when nearby terrain is incomplete. Movement remains unrestricted.

## Defined Minecraft light values

Version 0.16.21 centralizes emission values and state rules in light-rules.js. Lit lamps, candles, furnaces, oxidation variants of copper bulbs, waterlogged sea pickles, respawn-anchor charges, sensors and portals use explicit levels. Day/weather sky-light transitions follow the supplied Java Edition tables; midnight sky light is 4 and sealed underground sky light remains 0. Hostile spawning distinguishes block light from sky light, and crops/saplings require light level 9 to grow. Light-state changes update existing meshes without rebuilding them.

## Darker zero light

Version 0.16.21 keeps Minecraft�s nonlinear light brightness curve, reducing the added ambient floor from 3.5% to 0.3%. Full light remains unchanged. Held items use the same curve.

## Texture switch and optimization

Version 0.16.21 adds a labeled Bare Bones / Minecraft toggle switch. Native grass sides include the biome-tinted overlay; existing model nets use compatible Minecraft texture paths, including beds, rails and dragon heads. Texture decoding is parallelized. Successful instant lighting edits skip redundant regional recalculation. Browser tests found no unmapped texture keys used by the block model catalog and verified immediate cross-chunk placement/removal and roof changes.

## Instant lighting edits

Version 0.16.21 immediately relaxes affected light cells after a block edit, across chunk boundaries, and uploads lighting to affected existing meshes in the same call. Deferred regional calculations remain for initial/bulk reconciliation. Tested immediate torch placement/removal and opening/closing a sealed roof, including visible mesh light attributes.

## Lighting update

Version 0.16.21 propagates skylight and block light across chunk boundaries, including light removal. Lighting runs independently of pending meshes, uses a bounded frame budget, and updates existing light attributes only in affected sections. Sealed underground rooms receive no daylight or moonlight.

## Textures

The pause menu's **Use Minecraft textures** toggle switches to the supplied Minecraft texture set after reloading. The original textures remain bundled and are restored by switching it off. This preference is local to each browser. Resource pack uploading is no longer offered.

## 0.16.21 repairs

- Render plants and torches at distant LODs instead of omitting them.
- Preserve the existing models while switching block, item, entity and GUI textures.
- Prevent atlas aliases from overwriting replacement textures.
- Separate 0�15 skylight and block light; render darkness and local emission through vertex light attributes, with day changes handled by a shader uniform.
- Correct skylight above the terrain scan and add native lantern, soul light, end rod and other emissive block values.

## Visual and gameplay repairs (0.16.0)

- Held blocks/items use their models or transparent sprites; bows show drawing stages. Remote players show their selected item.
- Fixed azalea textures, head side UVs, zero-thickness dragon wing faces, and block-icon scaling.
- Chests animate their lid and latch without rebuilding chunks. Multiplayer chest viewer state and machine metadata are synchronized.
- End arrival initializes about 40 endermen. The exit uses a layered End portal surface rather than the experimental see-through effect.
- Void death returns to a safe Overworld spawn, with survival values reset. Spawn selection checks player collision. Full opaque blocks suffocate players and ordinary mobs; partial blocks do not.
- Creative can mine unbreakable blocks. Command results appear in local chat; public chat commands retain their broadcast behavior.
- Multiplayer entity snapshots include motion/animation and appearance state. Rendering remains interpolated, and networking remains subject to latency; this is not a guarantee of complete Minecraft multiplayer parity.

## Instant models, beds and chest loot (0.15.0)

Doors and levers update their existing vertex/normal/UV ranges immediately, rather than rebuilding or relighting a chunk. Door halves and multiplayer state changes share this path. Queued section builds sample the current state again before committing, and unchanged network power snapshots no longer trigger repeated remeshing.

Beds use separate head/foot nets from the supplied Bare Bones texture and the installed BedRenderer mattress/leg dimensions and UV offsets. Placement creates both halves, records facing, requires support under both halves, and breaking either half removes the partner. Existing template beds retain their original half/facing properties.

The [loot table viewer](https://www.minecraftmaps.com/tools/loot-tables) was checked for pool, roll, weight and function structure. The 56 Java 1.21.10 chest tables are bundled from the installed Minecraft client. Village houses, strongholds, dungeons, Nether fortresses, bastions and End cities use their corresponding pools; template block-entity loot references take priority. Contents roll once, use deterministic per-chest randomness, preserve collected/opened chest contents and distribute stacks among random slots. Player-placed chests stay empty. Unknown items are omitted without being replaced by unrelated items or redistributing their selection weight. Advanced loot functions not supported by the game's item system are retained as metadata, rather than claiming full vanilla enchantment/map functionality.

Verification: 40 lever/door orientation and state pairs; 100 toggles reuse the same buffers without queuing a remesh; all 56 loot tables tested with 20 seeds each; bed half/facing geometry and atlas references; syntax and portable build checks. This pass did not include an interactive gameplay performance measurement.

## Fountain, door-variant and torch corrections

Template water preserves Minecraft source/flow/falling levels. Downward flow takes priority over horizontal spread, with the vanilla three-source-neighbor exception. Plains and desert fountain fixtures remain contained after 240 flow ticks; vertical falls, four-way seven-block spread and source-removal drainage pass. Imported jungle door variants now share door interaction, paired placement/removal, collision, redstone and instant model updates. Floor/wall and redstone lit/unlit models use the installed Minecraft geometry and existing pack textures. Redstone torches strongly power the block above instead of all adjacent blocks, and count off-switches for burnout. Cross-checks: the installed FlowingFluid bytecode, [Minecraft's door behavior](https://www.minecraft.net/en-us/article/taking-inventory--door) and [Microsoft's redstone guide](https://learn.microsoft.com/en-us/minecraft/creator/documents/redstoneguide).

## Combat and mob portal travel

Combat follows the installed Minecraft Java Edition 1.21.10 client: weapon-specific recharge, weak rapid attacks, falling critical hits, sprint knockback, sword sweeps, Sharpness/Strength, armor/toughness, and stronger-hit damage immunity. The small bar below the crosshair shows attack recharge. Hold right click (mobile Use) to draw a bow; release to shoot a physical arrow. Shields block frontal attacks after 0.25 seconds, wear from blocking, and axes disable them for five seconds. Splash magic bypasses shields; poison cannot kill. Multiplayer punches and arrows use the same damage path.

Every supported living entity has corrected health, measured in HP (two HP per heart): cow 10, sheep 8, villager 20, zombie 20, creeper 20, skeleton 20, spider 16, witch 26, blaze 20, piglin 16, enderman 40, ghast 10, shulker 30, dragon 200. Normal-difficulty base melee damage: zombie 3, spider 2, piglin 5, enderman 7, blaze 6; projectile damage follows arrow speed, blaze fireballs deal 5 and shulker bullets deal 4. Closed shulkers have 20 armor and deflect arrows; zombies have 2 armor. Creeper explosions use power 3, ghast explosions power 1, TNT power 4; damage accounts for distance and cover. Existing saves retain each mob's fraction of health when migrating.

Mobs can enter Nether and End portals while you remain in either dimension, retaining their model, health, velocity and network identity. Nether arrivals have the vanilla 15-second cooldown after leaving the portal; the dragon cannot change dimensions.

Research: combat/attribute constants were verified using official mappings and bytecode from the installed Java 1.21.10 client. Cross-checks: [Microsoft's entity definitions](https://learn.microsoft.com/en-us/minecraft/creator/reference/source/vanillabehaviorpack_snippets/entities/enderman?view=minecraft-bedrock-stable) and [Mojang's portal issue documentation](https://bugs-legacy.mojang.com/browse/MC-151648). This targets the mechanics available in Voxel Frontier; it does not implement every vanilla enchantment or difficulty setting.

Loading screens cover terrain and asset preparation, New World creation, and multiplayer connection/world transfer. Dropped-item hit tests now include the camera, fixing the rendering exception that froze the view until the item was collected. Mobs, items, XP, projectiles, vehicles and falling blocks are parked per dimension during travel; mobs/items/XP/vehicles/falling blocks also persist in saves. Portal previews show destination entities and multiplayer avatars. Existing entities in other dimensions continue physics, wandering, breeding timers and animations at 20 ticks per second in retained terrain. They cannot target, collide with, or damage a player in another dimension. Portal previews build the full destination terrain and project and clip the aperture polygon before drawing, keeping it visible during close, angled camera movement while preserving foreground block occlusion. Touch Play/Join requests browser fullscreen; browsers without page fullscreen support can use a home-screen launch.

Chunk generation reuses noise samples. Mesh builds reuse unchanged lighting and use direct neighboring voxel reads; chunk streaming updates its search when the player changes chunks. Mob pathfinding uses a stable priority queue, sunlight checks cache column heights, and unchanged HUD/inventory contents stay in place. XP orbs share an instanced draw call. Multiplayer batches changed cells and fluid levels, and replays missed edits when a guest reconnects.

Drop and XP effects are drawn offscreen before play begins, avoiding first-use graphics stalls. XP meshes reuse their geometry and material. Block edits use a priority section rebuild with cached lighting; slower relighting follows in the background. Chunk borders and vertical section boundaries update together.

## Peer-to-peer multiplayer

In the pause menu, enter a name and click **Host world**. Send the room code to friends; they enter it and click **Join**. The host must keep the game open. Up to four players can build together. Joining uses a separate session world and does not overwrite the guest's single-player save. **Leave** returns to that save.

WebRTC carries world edits, avatars, containers, weather/time, mobs and item pickup. The included PeerJS library uses its public signaling service to establish the connection. Some restrictive networks cannot connect without a TURN relay; an error is shown if connection setup fails. Textured 3D players have animated limbs, collision, melee damage and knockback. The host simulates existing entities in visited dimensions and sends those dimensions to guests, including their portal views. New mobs spawn near the host in the host's current dimension. Keep backups before hosting an important world.


End entry places the player ten blocks above the central island surface. The End exit returns to the Overworld surface at X=0, Z=0. The End exit keeps its textured surface, while entry portal previews clip accurately when the camera jumps across the near plane.

Lighting and chunk remeshing now yield across frames after block changes. Rain and snow fall as 3D streaks in the world instead of a screen overlay. Press `/` while playing for a command bar. Supported single-player commands include `/help`, `/seed`, `/gamemode`, `/time`, `/weather`, `/tp`, `/give`, `/clear`, `/setblock`, `/fill`, `/summon`, `/kill`, `/gamerule`, and `/say`. Use `/help` in game for the current list. Worlds save in each browser separately, including when played from a shared URL.

The pause menu now has a saved render distance slider from two to eight chunks. Camera range, fog, and chunk streaming follow the selected distance. Dragon melee and arrow hits use the visible model parts instead of a distant center point, and player collision uses its torso shape. The leg joint rotations now account for the renderer's upward Y axis, keeping the feet connected and folded beneath the body.

## Play this build

Open `index.html` in Microsoft Edge or Chrome and click **Enter World**. Choose Survival for progression, Creative for building, or Spectator for exploration. The menu's **First steps & saving** section explains the starting loop. On desktop, **E** opens inventory and **Esc** opens the pause menu. On mobile, use the left movement pad, drag the world view to look, and use the labeled action buttons. Touch-drag inventory stacks; long-press a slot to split. Use **Download World Backup** to keep a portable copy; **Restore Backup** loads it later. New World replaces the current browser save, so back up first.

The optional **Experiments** toggle enables direct portal crossing and destination views. Each portal renders into a cropped view of its projected opening, so the destination image stays aligned as the camera moves. The preview uses the destination's lighting, fog distances, and color conversion. Portal crossings keep the source world ready for the return view, update the sky and camera in the same frame, and rearm as soon as the player clears the portal plane. The water exit assist raises the player through normal movement.

Portal windows now reuse their render targets during a crossing to avoid a blank allocation frame. A generated Nether portal is placed on a searched solid, lava-free site, and unsafe saved Nether arrival positions return to that safe portal. The Nether spans 128 blocks vertically. At most three ghasts can live at once, with rarer spawn attempts; punching an incoming fireball sends it along the player's view, and a returned hit can kill a ghast. The build includes 87 sounds extracted from the locally installed Minecraft assets, covering block materials, steps, mobs, combat, fluids, portals, containers, mechanisms, and the Otherside music disc. Instant portal crossings use a shortened cue.

Skeletons carry bows and fire modeled arrows. Dragon wing tips follow their parent wings during flight. Duplicate wooden pickaxe and sword recipes were removed. The test world has separate supply chests and additional redstone, snow, and workstation fixtures; enter `testworld` in the New World seed field to create it.

The dragon model uses five neck and twelve tail sections. Its pose equations for the neck, head, body, tail, wings, jaw, and legs are ported from the installed Minecraft 1.21.10 client and driven by a 20 Hz flight history. The End fight now includes circling, strafing fireballs, a landing approach, perching, a roar, lingering dragon breath, a charge, and takeoff. Ten destructible End crystals on bedrock capped obsidian pillars heal the dragon until destroyed. Ranged hits bounce off the perched dragon, and a boss health bar shows encounter progress. Flight paths and arena dimensions remain adapted to Voxel Frontier's world scale.

The central End island is larger, and its bedrock exit podium exists from the start of the fight. The portal surface appears only after the dragon dies. The dragon's wings and three-part legs follow the body pivot, matching the installed model hierarchy.

Eyes of Ender can now be inserted into any End portal frame. Filling all twelve frames in a 5×5 ring activates the portal, including rings built by the player. Creative mode can remove misplaced frames; replacing one clears its old eye state. Existing stronghold eye states remain compatible.

Block hardness, blast resistance, required tools, and mining levels now use per-block data from Minecraft 1.21.9 rather than broad material approximations. Mining uses the game's 30/100 tick divisor, tool efficiency, and water/airborne penalties; TNT checks blast resistance. Tool durability, speed, mining tier, damage bonus, repair material, and enchantability follow the supplied tables. The catalog also includes the missing tool and armor tiers. Equipped armor now uses defense, toughness, knockback resistance, and per-hit durability loss.

Container and workstation screens use the GUI textures from the installed Minecraft 1.21.10 client. Slot controls align with those textures. Furnace and brewing contents persist, smelting consumes fuel, and output slots protect their results. Workstation simulations run at ten updates per second instead of scanning every frame.

The core survival, crafting, storage, physics, rail test range, and texture catalog are covered by browser regression checks. Advanced systems remain adaptations: this is a playable browser sandbox, not complete Minecraft parity. Merchant offers, enchantment choices, smithing upgrades, and stonecutting recipes are limited to the game's supported catalog.


Redstone now includes working droppers, dispensers, hoppers, daylight detectors, targets, trapped chests, note blocks, weighted and wooden pressure plates, copper bulbs, tripwire, sculk sensors, crafters, trapdoors, lightning rods, lecterns, and chiseled bookshelves, and activator rails. Pistons use the uploaded 3D model pack's face UVs for the base and head, including the inner cavity and arm.

Pistons now face the player when placed, extend horizontally or vertically, push up to twelve movable blocks, displace entities, and break fragile components in their path. Sticky pistons pull one movable block when power ends. Redstone torches mount on top or on a wall, invert power from their support block after a short delay, emit light when lit, and burn out after rapid toggling.

Redstone dust now draws connected lines through straight runs and corners, climbs one-block steps, and updates its shape when nearby blocks change. Its signal follows the connected wire and drops by one level per dust block.

The witch hat layers now rotate around their own pivots and sit together on the head. Chest latches are centered, and the chest inventory icon is rendered from the textured chest model.

Redstone blocks provide constant power. Redstone torches invert power from their supporting block. Buttons and pressure plates give temporary signals; detector rails respond to minecarts. Repeaters add adjustable delay, comparators compare or subtract signals, and observers pulse when the block in front changes. Pistons extend through up to twelve movable blocks; sticky pistons pull one block back. Powered rails now use both powered textures from the supplied 3D Default pack. The switched lever uses the pack's separate on model.

Minecarts can be pushed into motion on rails or across the ground and break after two quick punches, dropping a minecart in Survival. Powered rails switch to their lit texture when energized. Levers tilt when switched. Rails that connect to a higher neighbor now use a rising 3D track and matching hover outline.

Open `index.html` in a recent desktop browser. The game works offline from the folder. It saves worlds in that browser's local storage.

## Controls

- WASD: move; mouse: look; Shift: sprint.
- Space: jump, swim upward, or climb a ladder. Release Space to sink in water. Move toward a shore while holding Space to climb out. X descends in water, on ladders, or in creative flight.
- Left click: hold to mine, or click to attack. Right click: place, eat, ignite a portal or fire, use a furnace, open a chest, trade with a villager, or use a crafting table. Hold right click with a shield selected or equipped in the offhand to block damage.
- 1-9 or mouse wheel: hotbar. E or I: inventory and 2x2 crafting. C: 3x3 crafting when near a crafting table. Esc: menu.
- Creative mode: F toggles flight; Space rises and X descends.
- Spectator mode: choose it in the pause menu. Fly with WASD, rise with Space, descend with Ctrl, and hold Shift to move faster. Pass through blocks and mobs without interacting, taking damage, collecting items, or attracting monsters. Switch back in the menu; the game moves you above ground if you switch while inside a block.
- Place a minecart on rails with right click, then right click the cart to board. W pushes off from rest, S brakes, and Shift dismounts. Boats also use right click to board and Shift to dismount. Powered rails accelerate moving carts when switched on by a lever or wire; unpowered powered rails brake them.
- Right click a button or lever to power a circuit. Right click a repeater to cycle its delay or a comparator to switch between compare and subtract modes.
- Place snow layers by right clicking snow while holding a snow layer item. Up to eight layers can stack. Mine layers with a shovel to collect snowballs; right click to throw a snowball.

## World and progression

The world extends from Y=-64 to Y=319 and has terrain biomes, cave networks, sparse ore veins, trees, villages, chests, a stronghold, the Nether, and the End. Overworld terrain now uses moderate hills rather than massive mountains, while still blending across biome edges. Chunk loading reuses cached climate and height samples and processes one new chunk per frame to keep controls responsive. Chunks use three distance-based detail levels: full geometry nearby, fewer shared leaf faces at medium distance, and fewer small decorative meshes in the outer ring. Detail changes have distance hysteresis and are rebuilt one chunk at a time. Mobs switch to simpler textured models when distant while retaining their normal physics and behavior. Craft better pickaxes to mine higher tier ores. A 4x5 obsidian frame ignited with flint and steel opens a Nether portal. An Eye of Ender reports the stronghold's coordinates and bearing. Insert Eyes of Ender into all twelve stronghold portal frames to activate the End portal. Defeating the dragon opens a bedrock-framed return portal.

The inventory has 36 empty slots when starting a new world. Its layout follows the player inventory screen, with four armor slots, an offhand slot, a player preview, a 2×2 crafting grid, 27 storage slots, and nine hotbar slots. Drag stacks between inventory, equipment, hotbar, chest, and crafting slots. Right click a stack to split it. The green recipe button lists recipes that fit the current grid; available recipes can place their ingredients in the grid. Oak, spruce, and birch logs craft their matching planks; recipes that call for planks accept mixed wood types, including the additional plank variants in the block catalog. Creative mode includes the full available item catalog in searchable categories. Some catalog items are currently available only in creative mode.

This update adds 100 texture-backed blocks on top of the previous catalog. It also adds lava buckets; hoes that till soil; seed planting and growing wheat; growing oak saplings; beds that set a respawn point and skip the night; opening doors; climbable ladders; shields; four pieces of equippable iron armor; and basic villager trading. Overworld lava generates in deep caves. Water and lava have distinct source and flowing states, spread downward and sideways, and reroute when blocks change. Water advances about every 0.25 seconds, while lava advances more slowly. Flow chooses the closest reachable drop when one is nearby and spreads in multiple directions on flat ground. Fluid surfaces update immediately without height interpolation. Flowing water pushes the player in its current, and waterfalls pull downward. Lava flows more slowly and has a shorter horizontal reach in the Overworld. Caves retain a thicker roof below nearby land, and ore chances vary by height: diamond and redstone favor deep layers, copper and lapis favor middle layers, and coal favors higher layers. The sky shifts through dawn, daytime, sunset, and night colors.

Animals now wander, follow held wheat, breed when fed, and flee after being hurt. Shears collect 1–3 wool from adult sheep; sheep regrow it after grazing. Villagers flee nearby monsters. Hostile mobs acquire targets only through a clear sight line and navigate around blocks. Skeletons fire physical arrows, blazes and ghasts launch fireballs, witches throw poison potions, and shulkers fire homing levitation bullets. Creepers hiss before exploding and defuse if the player retreats. Zombies and skeletons burn in open sunlight, while spiders become neutral in daylight unless provoked. Endermen turn toward a player who looks at their face, become hostile after a short stare, dodge arrows, and teleport to dry spaces after a hit or when exposed to rain or water. Piglins attack players without golden headgear and when nearby chests or gold blocks are disturbed; they barter when given gold ingots. Snowy trees plant their trunks into the thin snow layer so they meet the ground. Mobs and dropped items now fall, collide with blocks, and move in currents; punches impart a short knockback. Unsupported sand and gravel fall as moving blocks, while generated caves retain a thick roof below sand.

New world chooses a fresh random signed 32-bit seed when the seed box is blank. Entered text is hashed to a signed 32-bit seed; numeric seeds are normalized to the same format. New world clears the old inventory, world edits, chests, dimensions, dragon progress, respawn point, and player state before loading the new world.

Enter `testworld` in the New World seed box for a repeatable flat test range with a water basin and drain, a contained lava basin, a lever and redstone wire line powering a lamp, a rail loop with powered rails and levers, a stocked chest, workstations, and a Nether portal. Typing in the seed box does not trigger game shortcuts. The circuit uses 0–15 wire power and connected powered rails can carry power for up to eight segments. Snow layers have one to eight visible layers; their collision surface is one layer lower than their outline. A single layer can be walked through. Unsupported snow disappears, and nearby torches, lava, fire, or glowstone melt snow.

Rails follow connected tracks. Straight 3D rail models align their metal rails with east–west or north–south track. Corners have raised curved metal rails and angled wood sleepers, with the resource pack's curved rail texture beneath them. The same directions are used by cart movement and the simpler distant straight rail mesh.

Minecarts now continue past the end of a rail with their remaining momentum, then fall under gravity. An overhanging cart can remain supported by the last block until it moves clear of it. Off-rail carts collide with terrain, slow on the ground, and can settle onto a lower rail without jumping sideways to its center. Removing a rail also transfers its cart to physical motion; removing the support beneath lets it fall. A rider follows the cart during the fall.

The block, item, and mob textures came from the supplied Bare Bones 1.21.11 resource pack. Entity geometry and UV offsets now come from the supplied Template CEM archive. Each cuboid wraps the corresponding region of its full entity skin, including head, body, limb, and wool layers. The CEM PNGs are color coded UV guides; the Bare Bones skins provide the visible pixels. The block atlas and entity skins are embedded in `game.bundle.js` for local play. Keep the `textures/icons` and `textures/gui` folders with the game for inventory icons. The portal frame, crops, snow, farmland, bed, door, ladder, torch, chest, cactus, and portals use shaped models and matching hover outlines. Thin blocks sample the corresponding part of their texture. Nether generation includes a roof, lava sea, basalt deltas, soul sand valleys, warped and crimson forests, fungi, roots, vines, pillars, and ore veins.

Entity poses and state changes are adapted from the supplied FreshAnimations v1.10.5 pack for the CEM mob parts. Its OptiFine expression files are not executed directly. The FPS counter uses actual frame elapsed time; chunk generation and mesh rebuilding are budgeted across frames. Items retain their own icons instead of inheriting a stone fallback. Mobs collide with the player, and attacks and projectiles impart directional knockback. New terrain retains large caverns while making narrow tunnels and flooded cave regions less common.

The supplied 3D Default pack provides detailed geometry and UV layouts for chests, portal frames, torches, ladders, doors, rails, farmland, cactus, redstone wire, enchanting tables, brewing stands, anvils, levers, stonecutters, azaleas, bee nests, and flowers. The chest uses the pack's CEM assembly with the supplied Bare Bones chest skin. Shaped blocks without a corresponding model in that archive retain their existing custom geometry. Detailed models render nearby; simpler shapes are used at distance to limit frame spikes.

The playable page loads `game.bundle.js`. The Downloads folder contains only files needed to run the game and this guide; editable source is retained in the development workspace.

Mob rules were checked against Minecraft's [mob overview](https://www.minecraft.net/en-us/article/minecraft-mobs), [first-night guide](https://www.minecraft.net/en-us/article/how-survive-your-first-night-minecraft), [Enderman reference](https://www.minecraft.net/content/dam/minecraftnet/games/minecraft/software/Minecraft-Monstrous-Compendium-revised.pdf), [piglin guide](https://www.minecraft.net/sv-se/article/meet-piglins), and [wool guide](https://www.minecraft.net/pt-pt/article/block-week-wool). The behavior is adapted for this smaller browser world; detailed vanilla targeting, combat statistics, and every mob variant are not fully replicated.

This is a compact Minecraft-inspired game. The dragon, villages, mob behavior, recipes, fluid flow, and lighting are simplified for real-time browser play. It is not affiliated with Mojang or Microsoft.

Floor edits rebuild affected sections immediately; changed lighting is updated in bounded steps. Autosaves use asynchronous browser storage during play. Flowers retain the same daisy texture in their detailed and distant models.

Multiplayer includes the approved Metered client TURN credential for cross-network relay (UDP, TCP, and TLS). The account currently has a free trial allowance; connections requiring relay stop when its quota is exhausted. These client credentials are intentionally public in the uploaded browser build; no account administration API key is included.

Version 0.14.3 fixes the relay configuration being omitted from PeerJS options, accepts capitalized room codes, reconnects signaling after interruptions, and loads versioned scripts to avoid stale mobile caches. Relay tests assert actual selected local and remote TURN candidates. Refresh both devices and create a new host room after updating.

Version 0.15.0 adds Minecraft-style hunger, saturation, exhaustion, regeneration and starvation; chat and multiplayer commands (`/say`, `/msg`, `/tell`, `/w`, `/me`, `/list`); multiplayer syncing for redstone-powered block states and End crystal destruction; an End-return loading screen; faster chunk generation; and a loading-screen version label. New water updates are applied together per flow tick to keep simultaneous branches stable.


Entity selectors support type (including !type exclusions), distance ranges, name, limit, sort, x/y/z and dx/dy/dz boxes for /tp and /kill. Examples: /tp @e[type=minecraft:zombie,distance=..30] ~ ~ ~; /kill @e[type=minecraft:item]. Spawn eggs are in the creative inventory's Spawn eggs tab. Testworld's display mobs have noAI enabled and do not despawn with distance. The entity registry provides model, texture, health, speed, drops, onSpawn, onTick, onIdle, onDamage, and onInteract hooks; the item registry provides definition and onUse hooks. Existing Minecraft behavior routines are retained as shared systems.

## Repairs in 0.16.8

Corrected chest lid/interior, hopper, daylight detector, bed side and ceiling blossom rendering. Resource pack buttons use readable colors. Dynamic portals replaces the Experiments label. Fifteen animated atlas tiles use animation sheets from the supplied pack or installed Java assets. Lava ignites the player, with a fire overlay and extinguishing in water/rain.

Big dripleaves tilt under players and projectile hits, recover, and remain stable when powered. Bone meal grows dripleaves. Beds use one canonical item per existing color, place both halves, allow Overworld sleeping and respawn setting, and explode when used in the Nether/End. Water buckets evaporate in the Nether. Primed TNT remains visible during its fuse. Bow release also handles pointer release/cancellation; the browser context menu no longer interrupts charging. Dragon damage tests individual model parts; head hits retain full damage, and other parts take reduced damage. Perch/takeoff includes a watchdog for stalled flight.

Defeating the dragon creates a bedrock-framed End gateway. An ender pearl through it creates a linked gateway on the outer islands; entering the gateway block also transports the player. Destinations are saved and shared with the world. This implements the playable gateway path, not every detail of Java gateway generation, beam timing or entity transport. References: [End cities and gateways](https://www.minecraft.net/zh-hans/article/end-city), [dripleaf and spore blossom behavior](https://www.minecraft.net/pt-br/article/caves---cliffs--part-i-out-today-java).

### 0.16.8 runtime review

Fixed negative-coordinate chunk edge lighting and bed metadata cleared by batch placement. Paused single-player worlds now stop primed TNT and dripleaf simulation; multiplayer keeps running. Removed repeated native model resolution, per-frame held-camera projection updates and animated atlas mipmap generation. Failed sound loads terminate without recursive replay, and stale audio loads cannot overwrite a newly selected sound. Removed stale dripleaf state when its block is broken.

Verified with targeted audio, chunk-boundary and bed regressions, the shared actor/commands/network snapshot/instant model/fluid tests, and a browser smoke check covering paused TNT, native block models, animated atlas configuration and gateway creation. This is a focused runtime review, not a claim of complete Minecraft parity or a measured hardware FPS improvement.

### Nature catalog and responsiveness

Added 30 Minecraft plant items using native Java model geometry and Bare Bones textures: 14 small flowers, four tall flowers, tall grass, ferns, mushrooms, dry grasses, bushes, lily pads and cactus flowers. Tall plants place both halves together and are removed when their support is destroyed. Plants use appropriate soil, sand, water or cactus supports; grasses and ferns receive biome tint. New Overworld chunks generate biome-selected vegetation. Existing explored chunks are preserved. Nature items appear in the creative catalog and the test world's item chests/model gallery. This does not implement every plant-specific Minecraft growth, light or drop rule.

Block edits now hide old geometry and show exposed/replacement surfaces immediately, while section rebuilds remain budgeted. Inventory refreshes coalesce, icon images are decoded once, unchanged hotbar DOM is retained, and background rendering is reduced while the inventory is open. Browser regressions passed for all 36 plant block models/icons, tall-plant placement/support removal, immediate floor breaking and placement, temporary mesh cleanup, and coalesced inventory refreshes.

References: [Minecraft grass](https://www.minecraft.net/en-us/article/taking-inventory--grass), [Minecraft ferns](https://www.minecraft.net/pl-pl/article/fern), [Minecraft lily pads](https://www.minecraft.net/de-de/article/taking-inventory--lily-pad). Native geometry was read from the installed Java 1.21.10 assets.

### 0.16.8 environmental damage and textures

Cactus contact deals one health point through the normal damage/armor cooldown. Lava removes ordinary dropped items; netherite items survive. Rebuilt bed face crops for all 16 dye colors, corrected side orientation, and regenerated the registered bed/chest inventory icons. Bed item models include both complete halves; placement refreshes both halves after their state is recorded. Chest face crops now come directly from the uploaded Bare Bones pack, with the lid using the external top. Block materials display existing per-face grid lighting without a second Lambert lighting pass.

Verified cactus contact boundaries, lava drop removal and netherite survival, registered bed geometry/face availability/distinct colored icons, native plant models, instantaneous floor edits, inventory refresh coalescing and temporary mesh cleanup. Reference: [Netherite fire resistance](https://www.minecraft.net/en-us/article/taking-inventory--netherite-ingot).

### 0.16.8 inventory and startup

Initial page startup prepares terrain without showing a loading overlay; creating/joining worlds retains progress screens. Inventory hover names use an immediate visible tooltip. Q drops one hovered inventory item (or the selected hotbar item during gameplay); Ctrl+Q drops the stack. Dragging a stack outside the inventory drops it, and clicking outside with a cursor stack drops it. Restored the requested chest lid/interior texture arrangement. Block icon rendering no longer depends on Lambert lights; rebuilt every registered block icon from the current atlas and model.

### 0.16.8 runtime icons, vegetation and navigation

Block icons now render on demand from current model geometry and the active atlas, cache their result, and invalidate when a resource pack changes. A bounded queue avoids rendering the whole catalog in a single interaction. Enter World shows a progress screen and waits for terrain/effect readiness; initial page startup remains unobstructed. Flipped the bed mattress side tiles vertically.

Vegetation uses configured/placed feature data from installed Java 1.21.10: short-grass patches use 32 attempts, tall grass 96, ordinary flowers 64, horizontal spread 7 and vertical spread 3. Biome patch counts and rarity filters are retained, including rare tall-grass patches in plains/meadows. Neighboring patch origins are evaluated consistently across chunk boundaries. Default flower weights favor poppies 2:1 over dandelions; forest tall flowers and meadow flower regions use separate selections. This implements those rules on Voxel Frontier's terrain/biomes; Minecraft's exact noise/biome maps and every vegetation feature are not ported.

Navigation uses A* travel distance plus Java PathType penalties: water and nearby hazards 8, direct fire 16, lava/cactus/blocked/powder-snow nodes rejected by default, with per-entity overrides. Includes diagonal neighbors, obstacle corner checks and entity headroom. Searches are bounded to keep frame times predictable. Passive damage initializes panic, remembers the damage origin and selects a reachable escape route, then follows it at increased speed; the shared path search steers both pursuit and panic. Minecraft's complete goal scheduler and every species-specific navigation evaluator are not ported.

References: [official vegetation patch changes](https://feedback.minecraft.net/hc/en-us/articles/32412964700813-Minecraft-Beta-Preview-1-21-60-23), [official panic behavior](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/entityreference/examples/entitygoals/minecraftbehavior_panic?view=minecraft-bedrock-stable). Java defaults and patch settings were verified against local game assets and PathType bytecode.

### 0.16.8
- Flint and steel ignites supported block surfaces and mobs, retains portal/TNT ignition, and syncs mob ignition through multiplayer. Burning mobs display flames and take damage.
- Removed action notifications at the top; chat output remains available.
- Fixed short-grass textures and azalea models/selection outlines across detail levels, brightened block shading, added a textured sleep overlay, and enabled stepping onto slabs/stairs.
- Verified short-grass texture, azalea detail/outline consistency, slab stepping, and mob ignition/flames/damage in a browser with no page errors.


### 0.16.9
- Fixed missing atlas aliases for short grass and azalea textures; restored green biome tint.
- Directional structure blocks rotate to player facing when placed.
- Flower patches are three times rarer; grass patches twice as rare, with 60% fewer placement attempts. Existing vegetation remains in saved chunks.

### 0.16.10 loading and block updates
- Higher terrain/mesh budgets during loading; buried full cubes skip model resolution; cached lighting occlusion checks; nearest chunks mesh first.
- Preloads two additional chunk rings and prioritizes the player's facing direction. Movement is not blocked at unloaded boundaries.
- Restored detailed alpha-cutout oak, spruce, birch and flowering azalea leaf textures from the installed Minecraft 1.21.10 assets.
- Added a reusable due-tick/priority/insertion-ordered, deduplicated update scheduler and migrated delayed lava updates to it. Existing immediate neighbor notifications and deduplicated water/gravity queues remain; this is not a complete replacement of Minecraft's simulation engine.
- Research: https://learn.microsoft.com/en-us/minecraft/creator/reference/content/blockreference/examples/blockcomponents/minecraftblock_tick and Minecraft Java LevelTicks/ScheduledTick mappings.
- Verified scheduler ordering/delays/deduplication/budget, leaf alpha variation and browser regressions. Loading-only benchmark: 26.2s before / 21.9s after, Edge software WebGL; hardware timings differ.


### 0.16.11 inventory icons and scheduled updates
- Fire/cross-model icons resolve the block texture instead of defaulting to grass. Generated icons apply plant, leaf, water and redstone colors.
- Gravity checks now share the ordered scheduled-update queue with bounded processing and duplicate suppression, separately per dimension. Lava uses delayed scheduled checks.
- Same-state non-fluid block writes return without lighting/mesh/neighbor work; batch fluid cache invalidation runs once rather than per block.
- Browser checks passed for fire-icon UVs, green leaf icon colors, grass/azalea models, stepping and mob ignition. Scheduled queue tests passed. With the prefetch changes, nearby terrain prepared in 13.7 seconds in the same software-WebGL test (26.2 seconds baseline).

### 0.16.12 hotbar and leaves
- Fixed hotbar rendering exceptions for items without a color field; validated all nine slots with short grass selected.
- Generated model icons use face tint metadata and texture names, including plant/leaf colors, while leaving azalea wood untinted.
- Applied all nine leaf textures from Barebones Leaves Add-on. Leaf holes use alpha-test cutouts and opaque depth-writing pixels; preserved internal leaf faces across LODs.
- Research: https://learn.microsoft.com/en-us/minecraft/creator/reference/content/blockreference/examples/blockcomponents/minecraftblock_material_instances .
- Browser checks passed for hotbar, icon UVs/tint, grass/azalea models, stepping, scheduled gravity and burning mobs.


### 0.16.13 leaves, sprinting and settings
- Leaves without an adjacent air block use opaque variants; exposed leaves retain cutout transparency. Neighbor edits refresh this selection.
- Fleeing mobs run at 2.5 times normal movement speed by default; leg cycle speed follows fleeing speed with a wider sprint stride.
- Render distance supports 2–32 chunks, with an inline warning above 16.
- Consolidated grass block inventory entries and migrated saved inventory/chest stacks to the canonical grass block ID. Existing placed blocks remain compatible.
- Browser checks passed for enclosed/exposed/restored leaf UVs, slider limits/warning threshold, one grass item, hotbar, scheduled gravity and earlier regressions.


### 0.16.14 optimized light grid and faster fleeing
- Separate cached 0–15 sky-light and block-light arrays. Torch emission is 14, normal propagation loses one level, leaves attenuate sky light by one and water by two.
- Chunk vertices carry two light channels. Day/night adjusts a shader uniform, eliminating global day/night relighting and geometry rebuilds. Lighting edits rebuild only sections with changed light channels.
- Source: https://learn.microsoft.com/en-us/minecraft/creator/reference/content/blockreference/examples/blockcomponents/minecraftblock_light_emission and light_dampening.
- Fleeing animals default to 3.5 times normal speed, with matching animation cadence.
- Verified light levels, falloff, torch illumination at night, compiled grid shader/vertex attributes, and the previous browser regression checks without shader errors.

