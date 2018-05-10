# fast-css-split-webpack-plugin

Split your CSS to solve IE9 selector limitation problem.

Using [webpack] to generate your CSS is fun for some definitions of fun. Unfortunately the fun stops when you have a large app and need IE9 support because IE9 will ignore any more than ~4000 selectors in your lovely generated CSS bundle. The solution is to split your CSS bundle smartly into multiple smaller CSS files. Now _you can_.™ Supports source-maps.

## why faster?

The original css-split-webpack-plugin use [postcss](https://github.com/postcss/postcss) to split css, when css file size becomes larger, postcss will be very slow. fast-css-split-webpack plugin use [css-split](https://github.com/yibn2008/css-split) instead of postcss, provide 10 times faster performance of css spliting.

Performance benchmark: https://github.com/yibn2008/css-split#performance

## Installation

```sh
npm install --save fast-css-split-webpack-plugin
```

## Usage

Simply add an instance of `FastCSSSplitWebpackPlugin` to your list of plugins in your webpack configuration file _after_ `ExtractTextPlugin`. That's it!

```javascript
var ExtractTextPlugin = require('extract-text-webpack-plugin');
var FastCSSSplitWebpackPlugin = require('../');

module.exports = {
  entry: './index.js',
  context: __dirname,
  output: {
    path: __dirname + '/dist',
    publicPath: '/foo',
    filename: 'bundle.js',
  },
  module: {
    loaders: [{
      test: /\.css$/,
      loader: ExtractTextPlugin.extract('style-loader', 'css-loader'),
    }],
  },
  plugins: [
    new ExtractTextPlugin('styles.css'),
    new FastCSSSplitWebpackPlugin({size: 4000}),
  ],
};
```

The following configuration options are available:

**size**: `default: 4000` The maximum number of CSS rules allowed in a single file. To make things work with IE this value should be somewhere around `4000`.

**imports**: `default: false` If you originally built your app to only ever consider using one CSS file then this flag is for you. It creates an additional CSS file that imports all of the split files. You pass `true` to turn this feature on, or a string with the name you'd like the generated file to have.

**filename**: `default: "[name]-[part].[ext]"` Control how the split files have their names generated. The default uses the parent's filename and extension, but adds in the part number.

**preserve**: `default: false`. Keep the original unsplit file as well. Sometimes this is desirable if you want to target a specific browser (IE) with the split files and then serve the unsplit ones to everyone else.

**defer**: `default: 'false'`. You can pass `true` here to cause this plugin to split the CSS on the `emit` phase. Sometimes this is needed if you have other plugins that operate on the CSS also in the emit phase. Unfortunately by doing this you potentially lose chunk linking and source maps. Use only when necessary.

[webpack]: http://webpack.github.io/
[herp]: https://github.com/ONE001/css-file-rules-webpack-separator
[postcss]: https://github.com/postcss/postcss
[postcss-chunk]: https://github.com/mattfysh/postcss-chunk
