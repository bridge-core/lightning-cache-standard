# lightning-cache-standard

The lightning cache standard specifies how tools can load and store data from Minecraft projects for fast access inside of validations, auto-completions and other utilities.
This standard assumes that the project follows the [project-config-standard](https://github.com/bridge-core/project-config-standard)

## Cache Structure

All files are stored inside of a ".lightningCache" folder that can be located over the new "dataCache" field inside of the [config.json file](https://github.com/bridge-core/project-config-standard).

**Example:** `"dataCache": ".lightningCache/"`

The folder contains a JSON file for each of Minecraft's file types. Files without data can be omitted.

```
- .lightningCache/
    - entity.json
    - clientEntity.json
    - item.json
    - soundDefinition.json
    - block.json
    - ...
```

## Individual Cache Files

The structure of an individual cache file looks like this:

```typescript
interface LightningCache {
	formatVersion: '1.0.0'
	/**
	 * File path relative to the config.json file
	 *
	 * @example "./BP/entities/player.json"
	 */
	[filePath: string]: CacheEntry
}

/**
 * TODO: Cache keys need to be specified per file type
 * By design, there's no need for tools to specify a src offset
 *
 * Cache keys with no values in the current file can be omitted.
 * You may add additional cache keys and other tools writing to the cache need to keep them
 * as long as they match the structure defined below
 */
interface CacheEntry {
	[cacheKey: string]: (string | { value: string; offset: string })[]
}
```

## Example: Loading Player Events

This example does assume that the player.json file is named "player.json" and located at its default path to make the example simpler. Whenever a step fails, return an empty array as a sensible default in addition to other error handling. An exception to this rule is step eight where it is better to discard invalid cache values and keep valid ones.

1. Locate the .lightningCache folder over the project config
2. Try to load "player.json file
3. Parse file as JSON
4. Access the "./BP/entities/player.json" property
5. Access the "event" property on the cache entry
6. Map over received data to either return the string directly or the "value" of the "value" property on the object
7. Return processed data
