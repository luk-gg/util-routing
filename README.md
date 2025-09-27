# LUK.GG util-routing
Commonly used routing functions throughout LUK.GG APIs. 

## Setup
### Cloning an existing project
Simply add/update submodules via `git submodule update --init --recursive`.

### Building a new SvelteKit project
Include the submodule via `git submodule add https://github.com/luk-gg/util-routing` and modify `svelte.config.js` to include the following:
```js
const config = {
  kit: {
    // Override SvelteKit's default routes directory
    files: {
      routes: "util-routing/routes",
      appTemplate: "util-routing/app.html"
    },
    // Optional import aliases
    alias: {
      $client: path.resolve('.', 'game/client'),
      $server: path.resolve('.', 'game/server'),
    },
  },
};
```
You can delete SvelteKit's `src/routes` folder now. Every javascript file directly in `src/lib` will act as a route.

### Adding game data
Game data is typically placed in `game/client` or `game/server` to allow import aliases with `$client/{path}` and `$server/{path}` (see [example](https://github.com/luk-gg/bpsr-api/blob/main/src/lib/items.js#L2)).  
Assets like game textures and models can be placed in `game/client/{assetFolderName}` for now. They may be moved to a CDN in the future.

## Usage
`npm i` and `npm run dev` to start the vite server.  

### Filtering output using URL Search Params
If your output is an object, such as `{ 123: data, 124: data }`, you can use `key` such as [https://localhost:5173/items/?key=123]().  
If your output is an array of objects, you can *shallowly* filter by keys (inclusive), i.e. [https://localhost:5173/weapons/?cost=500&attack=1000]().  

> [!NOTE]
> Even if you are requesting only one entry (like `?id=1`), *all* entries will be computed before filtering down to that single entry. Therefore if there is an error in *any* entry (even if its not the one you requested), the server will throw an error and not render the route.

### Generating output JSONs
To output json files, visit `/output` or `npm run output` (assumes port 5173 is open). Files will be created in `json/en` (multiple locales currently not supported). By default, every javascript file will have its default export generated into a single JSON file (`src/lib/items.js default()` → `json/en/items.json`).  

When outputting arrays of objects, you can optionally generate a "brief" version that includes minimal data (name, icon, id...). This reduces payload size when fetching a large list.

Use the named export `entries_brief` to override the default export (`src/lib/items.js entries_brief()` → `json/en/items.json`). [entries_brief() definition example](https://github.com/luk-gg/saofd-api/blob/main/src/lib/utils/index.js#L5) and [usage example](https://github.com/luk-gg/saofd-api/blob/main/src/lib/accessories.js#L42).

The "full"/verbose version of the data will now be output into **individual files**, using the key `id`/`Id`, such as (`src/lib/items.js id:123` → `json/en/items/123.json`).

## Reasoning for including a Vite server
Vite includes a convenient function [import.meta.glob](https://vite.dev/guide/features.html#glob-import) which allows use of regex to import multiple files.  
Also I like debugging JSONs in the browser. You can output them manually with `writeFileSync` if you so please.
