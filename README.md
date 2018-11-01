<img style="display: block; margin: 5em 0 auto;" src="https://www.pagedmedia.org/wp-content/uploads/2018/11/pagedjs.png" alt="Paged.js logo - pagination in the browser"/>

Paged.js - Paged Media Tools
===========



## NPM Module
```sh
$ npm install pagedjs
```

```js
import { Previewer } from 'pagedjs';

let paged = new Previewer();
let flow = paged.preview(DOMContent, ["path/to/css/file.css"], document.body).then((flow) => {
	console.log("Rendered", flow.total, "pages.");
})
```

## Polyfill

Add the the `paged.polyfill.js` script to replace all `@page` css and render the html page with the Paged Media styles applied:

```html
<script src="https://unpkg.com/pagedjs/dist/paged.polyfill.js"></script>
```

Try the [polyfill with Aurorae](https://s3.amazonaws.com/pagedmedia/pagedjs/examples/polyfill.html).

By default the polyfill will run automatically as soon as the DOM is ready.
However, you can add an async `before` function or return a Promise to delay the polyfill starting.

```html
<script>
	window.PagedConfig = {
		before: () => {
			return new Promise(resolve, reject) {
				setTimeout(() => { resolve() }, 1000);
			}
		},
		after: (flow) => { console.log("after", flow) },
	};
</script>
```

Otherwise you can disable `auto` running the previewer and call `window.PagedPolyfill.preview();`
whenever you want to start.

```html
<script>
	window.PagedConfig = {
		auto: false
		after: (flow) => { console.log("after", flow) },
	};

	setTimeout(() => {
		window.PagedPolyfill.preview();
	}, 1000);
</script>
```

## Chunker
Chunks up a document into paged media flows and applies print classes.

Examples:

* Process the [first 50 pages of Moby Dick](https://s3.amazonaws.com/pagedmedia/pagedjs/examples/index.html).
* Upload and [chunk an Epub using Epub.js](https://s3.amazonaws.com/pagedmedia/pagedjs/examples/epub.html).

## Polisher
Converts `@page` css to classes, and applies counters and content.

Examples:

* Test [styles for print](https://s3.amazonaws.com/pagedmedia/pagedjs/examples/polisher.html).

### CLI

Command line interface to render out PDFs of HTML files using Puppeteer: [https://gitlab.pagedmedia.org/polyfills/pagedjs-cli](https://gitlab.pagedmedia.org/polyfills/pagedjs-cli).

## Modules

Modules are groups of handlers for that apply the layout and styles of a CSS module, such as Generated Content.

New handlers can be registered from `import { registerHandlers } from 'pagedjs'` or by calling `Paged.registerHandlers` on an html page.

```html
<script src="https://unpkg.com/pagedjs/dist/paged.polyfill.js"></script>
<script>
	class MyHandler extends Paged.Handler {
		constructor(chunker, polisher, caller) {
			super(chunker, polisher, caller);
		}

		afterPageLayout(pageFragment, page) {
			console.log(pageFragment);
		}
	}
	Paged.registerHandlers(MyHandler);
</script>
```

Handlers have methods that correspond to the hooks for the parsing, layout and rendering of the Chunker and Polisher. Returning an promise or `async` function from a method in a handler will complete that task before continuing with the other registered methods for that hook.

```js
// Chunker
beforeParsed(content)
afterParsed(parsed)
beforePageLayout(page)
afterPageLayout(pageElement, page, breakToken)
afterRendered(pages)

// Polisher
beforeTreeParse(text, sheet)
onUrl(urlNode)
onAtPage(atPageNode)
onRule(ruleNode)
onDeclaration(declarationNode, ruleNode)
onContent(contentNode, declarationNode, ruleNode)
```

## Setup
Install dependencies
```sh
$ npm install
```

## Development
Run the local dev-server with livereload and autocompile on [http://localhost:9090/](http://localhost:9090/)
```sh
$ npm start
```

## Deployment
Build the `dist` output
```sh
$ npm run prepare
```

## Testing

Testing for Paged.js uses [Jest](https://facebook.github.io/jest/en/) but is split into Tests and Specs.

### Tests

Unit tests for Chunker and Polisher methods are run in node using JSDOM.

```bash
npm run tests
```

### Specs

Specs run a html file in Chrome (using puppeteer) to test against CSS specifications.

They can also output a pdf and compare pages (one at a time) in that PDF with samples PDFs (saved as images).

To test the pdf output of specs, you'll need to install ghostscript locally.

```bash
brew install ghostscript
npm install ghostscript4js --no-save
```

Then run the jest tests in puppeteer.

```bash
npm run specs
```

To debug the results of a test in a browser you can add `NODE_ENV=debug`

```bash
NODE_ENV=debug npm run specs
```

To update the stored pdf images you can run

```bash
npm run specs -- --updateSnapshot
```

### Docker

A `pagedmedia/pagedjs` docker image contains all the dependencies needed to run the `pagedjs` development server, as well as the pdf comparison tests.

To build the image run

```bash
docker build -t pagedmedia/pagedjs .  
```

By default the container will run the development server with `npm start`

```bash
docker run -it -p 9090:9090 pagedmedia/pagedjs
```

The tests and specs can be run within the container by passing a `seccomp` file for Chrome and running `npm test`

```bash
docker run -it --security-opt 'seccomp=seccomp.json' pagedmedia/pagedjs npm test
```

## Contributors 

### Core team
The core team behind paged.js includes [Adam Hyde](https://adamhyde.net), [Julie Blanc](http://julie-blanc.fr/), [Fred Chasen](http://fchasen.com/) & Julien Taquet.

## Licence

MIT License (MIT), which you can read [here](LICENCE.md)