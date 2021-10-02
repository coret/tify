<p>
	<a href="https://github.com/tify-iiif-viewer/tify">
		<img src="https://demo.tify.rocks/public/img/tify-logo.svg" alt="TIFY" width="148" height="60">
	</a>
</p>

TIFY is a slim and mobile-friendly IIIF document viewer built with [Vue.js](https://vuejs.org/).

[Check out the demo](https://demo.tify.rocks/demo.html?manifest=https://manifests.sub.uni-goettingen.de/iiif/presentation/PPN857449303/manifest) and feel free to load your own manifests by changing the URL.

## Embedding TIFY

TIFY is available as an [npm package](https://www.npmjs.com/package/tify):

``` bash
npm install tify
```

To embed TIFY into your website:

1. Copy the contents of the `dist` directory to your server.
2. Add an HTML element serving as the container, which should have the following CSS applied:
	- `position: relative` or `position: absolute`
	- `width` and `height`
3. Include the TIFY JavaScript and CSS files.
4. Create a TIFY instance.

The required HTML looks something like this:

``` html
<link rel="stylesheet" href="tify-0.24.2.css">
⋮
<div id="tify" style="position: relative; width: 100%; height: 640px"></div>
<script src="tify-0.24.2.js"></script>
<script>
	new Tify({
		container: '#tify',
		manifestUrl: 'https://example.org/iiif-manifest.json',
	})
</script>
```

## Upgrading

If you are are upgrading from any previous version, please have a look at the [upgrading guidelines](UPGRADING.md).

## Options

TIFY takes an options object as its only parameter. While optional, you usually want to set the `container` and the `manifestUrl`.

- `container` (string or HTMLElement or null, default `null`): The HTML element into which TIFY is mounted. If set to `null`, TIFY is not mounted at all until `mount(_container_)` is called (see _API_ below).
- `language` (string, default `en`): The interface language, matching the translation filename without extension. [See which translations are available](https://github.com/tify-iiif-viewer/tify/tree/main/dist/translations) or add your own.
- `manifestUrl` (string): The URL of the IIIF manifest. If not set, the URL has to be provided via query parameter `manifest`, e.g. `https://example.org/?manifest=https://example.org/iiif-manifest.json`.
- `viewer` (object): An object with options for OpenSeadragon, TIFY’s image rendering component. [See its documentation](https://openseadragon.github.io/docs/OpenSeadragon.html#.Options) for all available options.
- `pageLabelFormat` (string, default `P : L`): Defines how page labels are displayed in the page selector and thumbnails view. The placeholder `P` is replaced by the physical page number (consecutive numbers starting at `1`) while `L` is replaced by the logical page label, which can be any string as set in the manifest.
- `pages` (array of 1-based integers, default `[1]`): An array of pages to display initially. Page numbers are physical numbers, starting at 1. This setting can be overridden by setting `pages` via URL query if `urlQueryKey` is set.
- `paramsStoredInUrlQuery` (array of strings, default `['filters', 'pages', 'panX', 'panY', 'rotation', 'view', 'zoom']`): An array of parameter keys to be stored in the URL query on change. Only has effect if `urlQueryKey` is set.
- `titleAffix` (string): If set, TIFY replaces the window title with the document title as defined in the manifest, appended by this string, e.g. ` | TIFY`.
- `translationsDirUrl` (string): The URL of the directory where TIFY finds its translations. TIFY tries to determine this URL automatically from its `<script>` element, but there may be cases where this fails, or you might want to load your own translation.
- `urlQueryKey` (string, only use characters `A…Z a…z 0…9 - _ . ~`): Read parameters from the URL query and reflect any changes, using the key provided. This also works with multiple concurrent instances, but each instance must use a unique key.
- `view` (string): The initially displayed view (panel).

An example with most available options set to non-default values:

``` js
new Tify({
	container: '#tify',
	language: 'de',
	manifestUrl: 'https://example.org/iiif-manifest.json',
	openSeadragon: {
		immediateRender: false,
	},
	pageLabelFormat: 'P (L)',
	pages: [2, 3],
	paramsStoredInUrlQuery: ['pages'],
	titleAffix: ' | TIFY',
	translationsDirUrl: '/translations/tify',
	urlQueryKey: 'tify',
	view: '',
})
```

## API

### `mount`

Mounts TIFY into the `container` element.

Parameters:
- `container` (string or HTMLElement): CSS selector pointing to a single HTML node or the node itself.

### `resetViewer`

Resets the viewer display options.

Parameters:
- `includingFiltersAndRotation` (boolean): By default, only pan and zoom are reset. Set to `true` to also reset image filters and rotation.

### `setPage`

Changes the active page or pages.

Parameters:
- `pageOrPages` (number or array of numbers): Provide a number to display a single page or an array of numbers to display multiple pages at once. If the number (or any of the numbers in the array) is smaller than `1` or greater than the number of pages in the document, the command is ignored.

Returns an array of the current pages or `false` if `pageOrPages` is invalid.

### `setLanguage`

Changes the frontend language and loads the associated translation.

Parameters:
- `language` (string, default `en`): The language code to load.  A JSON file containing the translations for this language must be present in `public/translations`. Strings with missing translation are displayed in English.

### `toggleDoublePage`

Switches from single to double page (“book view”) and vice versa.

Parameters:
- `force` (boolean, default `false`): Double page is forced on (`true`) or off (`false`).

### `toggleFullscreen`

Toggles fullscreen mode.

Parameters:
- `force` (boolean, default `false`): Fullscreen is forced on (`true`) or off (`false`).

### `toggleView`

Changes the active view.

Parameters:
- `name` (string): The name of view to toggle; `export`, `fulltext`, `help`, `info`, `scan` (only has effect on mobile), `thumbnails`, or `toc`.
- `force` (boolean, default `undefined`): Force the view on (`true`) or off (`false`).


### OpenSeadragon API

The object `viewer` exposes the full [OpenSeadragon API](https://openseadragon.github.io/docs/OpenSeadragon.html). If you want to control the scan view programmatically, the [methods of `viewer.viewport`](https://openseadragon.github.io/docs/OpenSeadragon.Viewport.html) are probably of interest.

API usage example:

``` js
const tify = new Tify({ manifestUrl: 'https://example.org/iiif-manifest.json' })
tify.mount('#tify')
tify.setPage([1, 12, 13])
tify.toggleView('thumbnails')
```

With the exception of `mount`, all API functions are only available after TIFY has been mounted.

## Build Setup

You need to have Node.js v12.0 or above, npm v5.5.1 or above (usually comes with Node.js) and git installed.

Install dependencies:

``` bash
npm install
```

Run in development mode with hot reload on `localhost:8080`:

``` bash
npm run serve
```

The manifest URL can be provided via option or query parameter (see above).

Build for production with minification:

``` bash
npm run build
```

The production build will be stored in `dist`.

## Running Tests

``` bash
# run unit tests
npm run unit

# run e2e tests
npm run e2e

# run all tests
npm test
```

---

Initiated by

<a href="https://www.sub.uni-goettingen.de/en/">
	<img src="https://demo.tify.rocks/supporters/sub-logo.svg" width="329" height="30" alt="Göttingen State and University Library (SUB Göttingen)"/>
</a>
