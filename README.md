# Webpack-Configure-Guidelines

## Step - by - step guidelines to configure the basic Front-End Build architecture for demo and practice vanilla projects (no frameworks)

1. Initialize NPM inside the project directory

   > `npm init`

2. Install the Webpack NPM dependencies required for a basic project build

   > `npm install --save-dev webpack webpack-cli css-loader styles-loader sass-loader node-sass @babel/core babel-loader @babel/plugin-proposal-class-properties @babel/preset-env terser-webpack-plugin mini-css-extract-plugin clean-webpack-plugin webpack-dev-server html-webpack-plugin`

3. Set folder and file srtucture - inside the project directory

   > `mkdir -p build src/common/scripts src/common/theme src/components src/templates && touch src/index.js webpack.config.js index.html`
   
   3.1 - If the project is using MVC architecture
      > - `mkdir -p src/scripts src/themes/compoenets src/themes/common && touch src/index.js src/scripts/controller.js src/scripts/view.js src/scripts/model.js src/themes/common/variables.scss src/themes/common/theme.scss src/themes/index.scss`

4. Configure webpack entry file, the output file and the build mode
   > - open in editor `./webpack.config.js`
   > - import the `path` node package
   > - set the `entry:` filen
   > - configure the `filename` and `path`
   > - set the build `mode` as `"development"`

```javascript
const path = require("path");
module.exports = {
  entry: "./src/index.js",
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "./build"),
    publicPath: ''
  },
  mode: "development",
  ...
};
```

5. Add "build" script
   > - open in editor `./package.json`
   ```json
     ...
      "test": "echo \"Error: no test specified\" && exit 1",
      "build": "webpack"
     }
     ...
   ```
6. Configure Style-Loader, CSS-Loader and SASS compiler
   > - open in editor `./webpack.config.js`
   > - import the `MiniCssExtractPlugin` with `const MiniCssExtractPlugin = require('mini-css-extract-plugin');`
   > - add the CSS load, compile, and minification plugin rules inside `module.exports.module.rules`
   > - configure the `MiniCssExtractPlugin` in `module.exports.plugins`

```javascript
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  ...,
  mode: "development",
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          MiniCssExtractPlugin.loader, 'css-loader'
        ]
      },
      {
        test: /\.scss$/,
        use: [
          MiniCssExtractPlugin.loader, 'css-loader', 'sass-loader'
        ]
      }
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: 'styles.css'
    })
  ]
}
```

7. Configure Babel to be able to use the latest JS features available
   > - open in editor `./webpack.config.js`
   > - add a JavaScript compile rule in `module.exports.module.rules`

```javascript
module.exports = {
  ...
  module: {
    rules: [
      ...
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: [ '@babel/env'],
            plugins: [ '@babel/plugin-proposal-class-properties' ]
          }
        }
      }
    ]
  },
  ...
}
```

8. Configure Terser to minify your bundle
   > - open in editor `./webpack.config.js`
   > - import the `TerserPlugin` with `const TerserPlugin = require('terser-webpack-plugin');`
   > - configure the `TerserPlugin` in `module.exports.optimization`

```javascript
const TerserPlugin = require('terser-webpack-plugin');

module.exports = {
  ...
  module: {
    rules: [
      ...
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: [ '@babel/env'],
            plugins: [ '@babel/plugin-proposal-class-properties' ]
          }
        }
      }
    ]
  },
  ...,
  optimization: {
    minimize: true,
    minimizer: [new TerserPlugin()],
  }
}
```

9. Configure the `CleanWebpackPlugin` and `HtmlWebpackPlugin`
   > - open in editor `webpack.config.js`
   > - import `CleanWebpackPlugin` with `const { CleanWebpackPlugin } = require('clean-webpack-plugin');`
   > - add the `CleanWebpackPlugin` to the list of plugins in `module.exports.plugins` by adding `new CleanWebpackPlugin()`

```javascript
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

module.exports = {
  ...,
    plugins: [
    new MiniCssExtractPlugin({
      filename: 'styles.css'
    }),
    new CleanWebpackPlugin(),
  ]
}
```

10. Setup and configure the continous hot-deploy dev server
    > - configure the build script
    > - add the development server configuration to './webpack.config.js'

```json
  "scripts": {
    ...
    "build": "webpack serve --config webpack.config.js --hot"
  }
```

```javascript
module.exports = {
  ...,
  mode: 'development',
  devServer: {
    contentBase: path.resolve(__dirname, './build'),
    index: 'index.html',
    port: 9000,
    writeToDisk: true
  },
}
```

## Setup for OPTIMAL browser caching

1. Add `[contenthas]` to output files

```javascript
...
module.exports = {
  ...
  output: {
    filename: "bundle.[contenthash].js",
  ...
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: 'styles.[contenthash].css'
    })
  ]
  ...
};
```

2. Install and import `HtmlWebpackPlugin` and `Handlebars`

   > - `$ npm install --save-dev html-webpack-plugin handlebars-loader` - to install the plugin and the loader
   > - `$ npm install --save handlebars` - to install the Handlebars NPM package
   > - add a Handlebars compile rule in `module.exports.module.rules`
   > - import `HtmlWebpackPlugin` with `const HtmlWebpackPlugin = require('html-webpack-plugin');`
   > - add the `HtmlWebpackPlugin()` to the list of plugins in `module.exports.plugins` by adding `new HtmlWebpackPlugin()`
   > - customize the `new HtmlWebpackPlugin()` outupt
   > - set the address of the Handlebars template file
   > - create the Handlebars template file - `$ mkdir -p src/templates && touch index.hbs`
   > - preapate the Handlebars template file

   ```javascript
        const HtmlWebpackPlugin = require('html-webpack-plugin');

        module.exports = {
          ...,
          module: {
            rules: [
                    {
                test: /\.hbs$/,
                use: [
                  'handlebars-loader'
                ]
              }.
            ]
          }
          plugins: [
            ...,
            new HtmlWebpackPlugin({
              template: './src/templates/index.hbs',
              title: 'Page title',
              minify: false,
              filename: 'subfolder/custom_filename.html',
              // subfolder is optional
              description: 'Some lorem description'
              // title and description are variables to be used in the handlebars template
            })
          ]
        }
   ```

   ```html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="UTF-8" />
       <meta name="viewport" content="width=device-width, initial-scale=1.0" />
       <meta http-equiv="X-UA-Compatible" content="ie=edge" />
       <title>{{htmlWebpackPlugin.options.title}}</title>
       <meta
         name="description"
         content="{{htmlWebpackPlugin.options.description}}"
       />
     </head>
     <body></body>
   </html>
   ```

## Setup for Bulma as CSS framework

1. Install Webpack, CSS, Sass and Bulma dependencies

   > - `npm install --save-dev bulma css-loader extract-text-webpack-plugin@next mini-css-extract-plugin node-sass sass-loader style-loader webpack webpack-cli`

2. Create theme file

   > - `touch ./src/common/theme/theme.scss`

3. Inside `theme.scss` add

```css
@charset "utf-8";
@import "../../../node_modules/bulma/bulma.sass";
```

NOTE - Make sure the path leads to the `bulma.css` inside `node_modules` direcotry

4. Inside the Webpack entry JS file (Eg. index.js) add

```javascript
import "./common/theme/theme.scss";
```

NOTE - Make sure the path leads to the new `theme.scss` file

5. EXTRA - For webperformance and smaller css bundle file import only the required bulma scss file
Eg.
```css
@charset "utf-8";

// Import a Google Font
@import url("https://fonts.googleapis.com/css?family=Nunito:400,700");

// Set your brand colors
$purple: #8a4d76;
$pink: #fa7c91;
$brown: #757763;
$beige-light: #d0d1cd;
$beige-lighter: #eff0eb;

// Update Bulma's global variables
$family-sans-serif: "Nunito", sans-serif;
$grey-dark: $brown;
$grey-light: $beige-light;
$primary: $purple;
$link: $pink;
$widescreen-enabled: false;
$fullhd-enabled: false;

// Update some of Bulma's component variables
$body-background-color: $beige-lighter;
$control-border-width: 2px;
$input-border-color: transparent;
$input-shadow: none;

// Import only what you need from Bulma
@import "../node_modules/bulma/sass/utilities/_all.sass";
@import "../node_modules/bulma/sass/base/_all.sass";
@import "../node_modules/bulma/sass/elements/button.sass";
@import "../node_modules/bulma/sass/elements/container.sass";
@import "../node_modules/bulma/sass/elements/title.sass";
@import "../node_modules/bulma/sass/form/_all.sass";
@import "../node_modules/bulma/sass/components/navbar.sass";
@import "../node_modules/bulma/sass/layout/hero.sass";
@import "../node_modules/bulma/sass/layout/section.sass";
```
Eg. - if only buttons are used from Bulma
```css
  @import "../node_modules/bulma/sass/elements/button.sass";
```
