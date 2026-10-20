# Survivor AI

**Made by Iva Petrova**

My Game AI exam project: an AI-controlled character that tries to survive as long as possible against the zombies provided by my teacher.

The agent's brain lives in `StudentPerceptor`. Every tick it decides what to do: move around, grab items, heal, or deal with nearby zombies.

## Decision making
The agent checks a priority list from top to bottom and runs the first behavior that applies:

1. **Death**: if stamina or HP reaches 0, the pawn is dead
2. **Garbage cleanup**: remove used items
3. **Purge zone**: escape the purge zone
4. **Heal**: use a medkit when injured
5. **Eat**: eat food when stamina is low
6. **Urgent loot**: go for items when HP or stamina is low
7. **Flee**: run from zombies if the pawn has no weapon
8. **Fight**: shoot zombies if the pawn has a weapon
9. **Loot**: collect items when the inventory has space
10. **Explore**: visit unvisited houses

## Enemy handling
- **Fight (armed):** kites Heavy zombies (backs away while shooting) and rushes all others via the navmesh. Face steering rotates the pawn toward the target each tick, then it fires via line trace.
- **Flee (unarmed):** sprints using weighted escape vectors and predicts zombie movement. Skips fleeing if the pawn is inside a house.

## Inventory
- **Sensing:** items are detected through the AI perception system and cached in `m_PerceivedItems`. A cleanup timer removes stale entries every 0.5s.
- **Pickup:** items are scored by priority (Urgent to Skip) based on current health/stamina ratios. Unreachable items are blacklisted so the agent doesn't get stuck.
- **Usage:** medkits and food are only used when they won't overshoot max health/stamina. Depleted items are removed immediately.
- **Cleanup:** a dedicated behavior tree branch runs every tick to evict zero-value items and keep slots free.

## Movement
### Steering behaviors
- **Arrive:** used in `LootAction` to smoothly slow down toward an item
- **Flee:** used in `PurgeZoneAction` (escape the zone center) and `FightAction` (kiting Heavy zombies)
- **Evade:** used in `FleeAction`, predicting future zombie positions before fleeing
- **Face:** used in `FightAction` to rotate the pawn toward a zombie before firing

### Blended steering
Used in `FleeAction`. Each nearby zombie contributes a flee vector weighted by zombie tier and inverse distance. The combined vector steers the agent away from all nearby zombies at once, with heavier and faster threats pushing harder.

### Exploration
- Visits all unvisited houses, closest first
- Remembers visited houses and never repeats them
- If stuck on geometry, wanders in a random direction briefly to break free, then resumes navigation

## High score
Best survival times: **01:58**, 01:44, 01:36, 01:13, 01:10.

## More details
See the PowerPoint (`ZombieAI_PetrovaIva.pptx`) for the full breakdown of how everything works.
