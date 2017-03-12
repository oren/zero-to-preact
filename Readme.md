# zero-to-preact

1. install deps
1. declare config
1. write app code

Create .gitignore
```
/node_modules
/build
.DS_Store
```

(Install Webpack development dependencies)

Run
```
npm init -y
npm install --save-dev webpack webpack-dev-server babel-core babel-loader babel-preset-es2015 babel-plugin-transform-react-jsx preact
```

Add to package.json (inside scripts)
```
"build": "webpack",
"start": "webpack-dev-server --progress --hot --inline"
```

Create webpack.config.js
```
var path = require('path');

module.exports = {
	// entry file - starting point for the app
	entry: './src',

	// where to dump the output of a production build
	output: {
		path: path.join(__dirname, 'build'),
		filename: 'bundle.js'
	},

	module: {
		rules: [
			{
				test: /\.jsx?/i,
				loader: 'babel-loader',
				options: {
					presets: [
						'es2015'
					],
					plugins: [
						['transform-react-jsx', { pragma: 'h' }]
					]
				}
			}
		]
	},

	// enable Source Maps
	devtool: 'source-map',

	devServer: {
		// serve up any static files from src/
		contentBase: path.join(__dirname, 'src'),

		// enable gzip compression:
		compress: true,

		// enable pushState() routing, as used by preact-router et al:
		historyApiFallback: true
	}
};
```

Create src folder

Add a static `index.html` that the browser will hit when we request `http://localhost:8081`. It just contains a `<script>` tag pointing to the `bundle.js` file Webpack outputs.

Create src/index.html
```
<!DOCTYPE html>
<html>
	<head>
		<title>Hello World</title>
	</head>
	<body>
		<script src="/bundle.js" async></script>
	</body>
</html>
```

Add our entry point - `src/index.js`. This is where webpack starts bundling, and what gets invoked in the browser when we're ready to go. For now it's just going to import Preact and an `App` component we haven't written, and render it into `<body>`.

Create src/index.js
```
// This is the entry file, which kicks off all rendering.
//
// We import h() here because that's the function our JSX elements transpile to.
// That is to say - this:
//     <div a="b">foo</div>
// ... is converted to this:
//     h('div', { a: 'b' }, 'foo')

import { h, render } from 'preact';
import App from './components/app';

render(<App />, document.body);
```

Add an App component, and a little Hello component it renders to demonstrate the two types of Preact components - classes and functions (respectively).

Create src/components/app.js
```
import { h, Component } from 'preact';
import Hello from './hello';

export default class App extends Component {
	render() {
		return (
			<div class="app">
				<h1>Hello!</h1>
				<Hello />
			</div>
		);
	}
}
```

Create src/components/hello.js
```
import { h } from 'preact';

export default () => (
	<p class="hello">
		Hello, cruel world!
	</p>
);
```

Let's go crazy and add Hot Module Replacement!
All we have to do is move our import of `App` and the `render()` call into a function, so that we can re-import changes as they get sent to the browser and re-render the new tree of components. We'll call that function so that we get an initial render like we had before, but then also pass it to Webpack to be called when we get updated components.


If (module.hot) module.hot.accept('./components/app', init);
says "when components/app changes, run the init function and the init function re renders our app. it's just a cherry on top
sorry, anything within components/app **

modify src/index.js
```
// This is the entry file, which kicks off all rendering.
//
// We import h() here because that's the function our JSX elements transpile to.
// That is to say - this:
//     <div a="b">foo</div>
// ... is converted to this:
//     h('div', { a: 'b' }, 'foo')

import { h, render } from 'preact';


// this holds our rendered root element so we can re-render in response to HMR updates.
let root;


// Making our app's initialization a function means it's repeatable.
function init() {
	// HMR requires that this be a require()
	let App = require('./components/app').default;

	// render the app and save the new root element:
	root = render(<App />, document.body, root);
}


// initial render!
init();


// If this is webpack-dev-server, set up HMR :)
if (module.hot) module.hot.accept('./components/app', init);
```

Run
```
npm run start
npm run build
```

## Notes:

with webpack-dev-server, everything runs from memory

