# Webpack Setup

npm i --save-dev webpack webpack-cli

`touch webpack.config.js`

```
// Webpack uses this to work with directories
const path = require('path');

	// This is main configuration object.
	// Here you write different options and tell Webpack what to do
	module.exports = {

		// Path to your entry point. From this file Webpack will begin his work
		entry: './assets/js/app.js',

		// Path and filename of your result bundle.
		// Webpack will bundle all JavaScript into this file
		output: {
			path: path.resolve(__dirname, 'dist'),
			filename: 'scripts.js'
		},

		// Default mode for Webpack is production.
		// Depending on mode Webpack will apply different things
		// on final bundle. For now we don't need production's JavaScript 
		// minifying and other thing so let's set mode to development
		mode: 'development' | "production" ! "none"
	};
```

got to package.json and change "scripts"
	```
	"scripts": {
		"dev": "webpack --config webpack.config.js"
	},
	```


## Webpack Dev Server

`npm i webpack-dev-server -D`

got to package.json and change "scripts"

```
	"scripts": {
		...

		"start": "webpack-dev-server"
	},
```
set options in webpack.config.js


_publicPath_: bundled files path

_contentBase_: [array] where to watch updated files

```
	...
	devServer: {
		port: 8080,
		publicPath: '/dist/',
		contentBase: [
			path.join(__dirname, '/'),
			path.join(__dirname, 'assets')
		],
		watchContentBase: true
	},
	...
```

## Resolve common extensions

don't write extension while importing modules
in webpack.config.js

```
	module.exports {
		...
		resolve: { extensions: ['.js', '.ts'] },
		...
	}
```

## Transpile ES6+ to ES5

`npm i babel-loader @babel/core @babel/preset-env -D`

add babel-loader to module's rules

```
	...
	module: {
		rules: [
			{
				test: [/.js$/],
				exclude: /(node_modules)/,
				use: {
					loader: 'babel-loader',
					options: {
						presets: ['@babel/preset-env']
					}
				}
			}
		]
	}
	...
```

## Extract all styles into a single file

`npm i mini-css-extract-plugin -D`

in webpack.config.js

```
....
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

...
module.exports = {
	module: {
		rules: [
			{
				test: [/.css$|.scss$/],
				use: [
					MiniCssExtractPlugin.loader,
					'css-loader',
					'sass-loader'
				]
			}
		]
		},
		plugins: [
			new MiniCssExtractPlugin({
				filename: 'style.css'
			})
		]
	},
```

## Convert SCSS to CSS

`npm i sass sass-loader postcss-loader css-loader -D`

in webpack.config.js

```
	...
	module: {
		rules: [
			...
			{
				// Apply rule for .sass, .scss or .css files
				test: /\.(sa|sc|c)ss$/,

				// Set loaders to transform files.
				// Loaders are applying from right to left(!)
				// The first loader will be applied after others
				use: [
					{
						// This loader resolves url() and @imports inside CSS
						loader: "css-loader",
					},
					{
						// Then we apply postCSS fixes like autoprefixer and minifying
						loader: "postcss-loader"
					},
					{
						// First we transform SASS to standard CSS
						loader: "sass-loader"
						options: {
							implementation: require("sass")
						}
					}
				]
			}
			...
		]
	}
	...
```

### PostCSS

apply transformaitons like autoprefixer or cssnano

`npm i autoprefixer cssnano -D`

create config file for postcss > postcss.config.js

```
	if(process.env.NODE_ENV === 'production') {
		module.exports = {
			plugins: [
				require('autoprefixer'),
				require('cssnano'),
				// More postCSS modules here if needed
			]
		}
	}
```

### Export CSS file

`$ npm i --save-dev mini-css-extract-plugin`

in webpack.config.js

```
	const MiniCssExtractPlugin = require("mini-css-extract-plugin");

	plugins: [
		...
		new MiniCssExtractPlugin({
			filename: "bundle.css"
		})
		...
	]
```

update scss test

```
{
      test: /\.(sa|sc|c)ss$/,
      use: [
             {
               // After all CSS loaders we use plugin to do his work.
               // It gets all transformed CSS and extracts it into separate
               // single bundled file
               loader: MiniCssExtractPlugin.loader
             }, 
             {
               loader: "css-loader",
             },
             /* ... Other loaders ... */
           ]
}
```

## Clearing Dist folder via plugin

`npm i clean-webpack-plugin -D`

in webpack.config.js

```
	const { CleanWebpackPlugin } = require('clean-webpack-plugin');

	module.exports = {
		...
		plugins: [
			...
			new CleanWebpackPlugin()
		]
	}
```
* mess up with dev-server

## Clearing Dist folder via npm

in package.json

```
	...
	"scripts": {
		...
		"dev" : "rimraf dist && webpack"
		...
	}
	...
```