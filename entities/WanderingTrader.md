# Wandering Trader
Wandering traders are an entity that spawns randomly around a player.
The wandering trader can be right clicked and it will offer to trade items.
Its class name is `WanderingTraderEntity`. Its id is `minecraft:wandering_trader` and it uses custom spawning mechanics.
## Spawning
Spawning of wandering traders is handled by the `WanderingTraderManager`. `WanderingTraderManager` is a `SpecialSpawner`
Here is a description of the tick method of `WanderingTraderManager`, `WanderingTraderManager.spawn()`
- Check if trader spawning is enabled (`doTraderSpawning` gamerule)
- Lower `this.spawnTimer`
- If `this.spawnTimer` is 1200 ticks (1 minute)
   - Lower `this.spawnDelay` by 1200
   - Save `this.spawnDelay` to `level.dat`
- If `this.spawnDelay` reaches 0
   - Reset to 24000 ticks (20 minutes)
   - If mob spawning is enabled `doMobSpawning`
      - Increase `this.spawnChance` by 25% up to a limit of 75%
      - Save `this.spawnChance` to `level.dat`
      - Do random check with success rate of `this.spawnChance`
        - Try spawn `this.trySpawn()`
          - Get random living player as target
          - If no player, reset `this.spawnChance` to 25
          - Do 1 in 10 random check
            - Target position is the position of target, or position of a village bell if it is within 48 blocks
            - Get a spawn position within a range of 48 from the target position `WanderingTraderManager.getNearbySpawnPos()`
              - Repeat 10 times:
                - Pick random x integer position
                - Pick random z integer position
                - Get highest non-air block at that x and z
                - Validate spawn location `SpawnLocationType.ON_GROUND`
                  - Check that block allows spawning ([BPE List](https://joakimthorsen.github.io/MCPropertyEncyclopedia/?selection=variants,spawnable&filter=(spawnable:Polar%20Bear%20Only,Ocelots%20and%20Parrots%20Only,No,Fire-Immune%20Mobs%20Only)#))
                  - Check that blocks at feet and head are clear for spawning (Should never fail) `SpawnLocation.isSpawnPositionOk()`
                - If spawn location is valid, return it
                - If no valid spawn location is found return null
            - If spawn position is not null and \
              wandering trader would not suffocate there and \
              make sure the biome has wandering trader spawns: 
              - Wandering trader attempts to spawn `EntityType.spawn()`
              - If wandering trader spawns correctly
                - It will spawn 2 trader llamas `this.spawnLlama()` -> `TraderLlamaEntity`
                  - The llama position is chosen by same algorithm as wandering traders except with a radius of 4 centered around the trader `WanderingTraderManager.getNearbySpawnPos()`
                  - Attach leash to llama
                - Set wandering trader UUID in level.dat `LevelProperties.setWanderingTraderId()`
                - Set wandering trader despawn delay to 40 minutes (48000 ticks) ``
                - Set the wandering trader's target to the player position

## Trades


To get their trades wandering traders use two lists of trades. 5 trades are chosen from the following list, then one trade is chosen from the second list.

| Item               | Emerald Price | Amount | Max Uses |
|--------------------|---------------|--------|----------|
| SEA_PICKLE         | 4             | 1      | 5        |
| SLIME_BALL         | 4             | 1      | 5        |
| GLOWSTONE          | 2             | 1      | 5        |
| NAUTILUS_SHELL     | 5             | 1      | 5        |
| FERN               | 1             | 1      | 12       |
| SUGAR_CANE         | 1             | 1      | 8        |
| PUMPKIN            | 1             | 1      | 4        |
| KELP               | 3             | 1      | 12       |
| CACTUS             | 3             | 1      | 8        |
| DANDELION          | 1             | 1      | 12       |
| POPPY              | 1             | 1      | 12       |
| BLUE_ORCHID        | 1             | 1      | 8        |
| ALLIUM             | 1             | 1      | 12       |
| AZURE_BLUET        | 1             | 1      | 12       |
| RED_TULIP          | 1             | 1      | 12       |
| ORANGE_TULIP       | 1             | 1      | 12       |
| WHITE_TULIP        | 1             | 1      | 12       |
| PINK_TULIP         | 1             | 1      | 12       |
| OXEYE_DAISY        | 1             | 1      | 12       |
| CORNFLOWER         | 1             | 1      | 12       |
| LILY_OF_THE_VALLEY | 1             | 1      | 7        |
| WHEAT_SEEDS        | 1             | 1      | 12       |
| BEETROOT_SEEDS     | 1             | 1      | 12       |
| PUMPKIN_SEEDS      | 1             | 1      | 12       |
| MELON_SEEDS        | 1             | 1      | 12       |
| ACACIA_SAPLING     | 5             | 1      | 8        |
| BIRCH_SAPLING      | 5             | 1      | 8        |
| DARK_OAK_SAPLING   | 5             | 1      | 8        |
| JUNGLE_SAPLING     | 5             | 1      | 8        |
| OAK_SAPLING        | 5             | 1      | 8        |
| SPRUCE_SAPLING     | 5             | 1      | 8        |
| CHERRY_SAPLING     | 5             | 1      | 8        |
| MANGROVE_PROPAGULE | 5             | 1      | 8        |
| RED_DYE            | 1             | 3      | 12       |
| WHITE_DYE          | 1             | 3      | 12       |
| BLUE_DYE           | 1             | 3      | 12       |
| PINK_DYE           | 1             | 3      | 12       |
| BLACK_DYE          | 1             | 3      | 12       |
| GREEN_DYE          | 1             | 3      | 12       |
| LIGHT_GRAY_DYE     | 1             | 3      | 12       |
| MAGENTA_DYE        | 1             | 3      | 12       |
| YELLOW_DYE         | 1             | 3      | 12       |
| GRAY_DYE           | 1             | 3      | 12       |
| PURPLE_DYE         | 1             | 3      | 12       |
| LIGHT_BLUE_DYE     | 1             | 3      | 12       |
| LIME_DYE           | 1             | 3      | 12       |
| ORANGE_DYE         | 1             | 3      | 12       |
| BROWN_DYE          | 1             | 3      | 12       |
| CYAN_DYE           | 1             | 3      | 12       |
| BRAIN_CORAL_BLOCK  | 3             | 1      | 8        |
| BUBBLE_CORAL_BLOCK | 3             | 1      | 8        |
| FIRE_CORAL_BLOCK   | 3             | 1      | 8        |
| HORN_CORAL_BLOCK   | 3             | 1      | 8        |
| TUBE_CORAL_BLOCK   | 3             | 1      | 8        |
| VINE               | 1             | 1      | 12       |
| BROWN_MUSHROOM     | 1             | 1      | 12       |
| RED_MUSHROOM       | 1             | 1      | 12       |
| LILY_PAD           | 1             | 2      | 5        |
| SMALL_DRIPLEAF     | 1             | 2      | 5        |
| SAND               | 1             | 8      | 8        |
| RED_SAND           | 1             | 4      | 6        |
| POINTED_DRIPSTONE  | 1             | 2      | 5        |
| ROOTED_DIRT        | 1             | 2      | 5        |
| MOSS_BLOCK         | 1             | 2      | 5        |
One of the following will be chosen as the last trade for the wandering trader

| Item                 | Emerald Price | Amount | Max Uses |
|----------------------|---------------|--------|----------|
| TROPICAL_FISH_BUCKET | 5             | 1      | 4        |
| PUFFERFISH_BUCKET    | 5             | 1      | 4        |
| PACKED_ICE           | 3             | 1      | 6        |
| BLUE_ICE             | 6             | 1      | 6        |
| GUNPOWDER            | 1             | 1      | 8        |
| PODZOL               | 3             | 3      | 6        |
