# cordova-template-webpack-ts-scss 03.0

This template is designed to make it easy for you to write ES2015+, TypeScript, and SCSS code, and then bundle that code using webpack v2. It will do this automatically each time you run any Cordova/PhoneGap command that triggers the `prepare` phase.

## Requirements

* Node v5 or better

* Cordova / PhoneGap CLI installed

## Usage

You can create a project using this template as follows:

```bash
$ cordova create hello com.example.hello hello --template cordova-template-webpack-ts-scss
```

Once the project has been created, you'll need to add a platform, like so:

```bash
$ cd hello
$ cordova platform add --save browser
```

The act of adding a platform will automatically trigger the first `prepare`, and when the process is finished, the example code supplied with this template will have been transformed automatically.

### Project Structure

This template uses an "external" structure for all transformed assets. This means there will be two directories that contain code:

* `www.src` &mdash; this is where your ES2015+, TypeScript, SCSS files, and all other assets live
* `www` &mdash; after `prepare` this will store copied assets and transformed files; **you should not make changes to this directory**

Within `www.src`, the following structure is assumed:

```text
www.src/
    index.html                          # your index.html file; app's entry point
    js/                                 # JavaScript files that don't need transpilation
    es/                                 # ES2015+ files
        index.js                        # ... App's javascript entry point
    ts/                                 # TypeScript files; if present overrides es/
        index.ts                        # ... App's typescript entry point; if present overrides es/index.js
    css/                                # CSS stylesheets that don't need transformed
    scss/                               # SCSS stylesheets
        styles.css                      # ... Primary styles; other styles need to be imported
    html/                               # HTML files
    img/                                # Image files
    lib/                                # Library files (perhaps frameworks)
    vendor/                             # Vendor files

```

### Transformations

The following transformations occur just prior to the `prepare` phase.

Sibling     |      Entry Point           | Output
-----------:|:---------------------------|:------------------
TypeScript  | `www/ts/index.ts`          | `www/js/bundle.js`
ES2015      | `www/es/index.js`          | `www/js/bundle.js`
SCSS        | `www/scss/styles.scss`     | `www/css/bundle.css`

External    |      Entry Point           | Output
-----------:|:---------------------------|:------------------
TypeScript  | `www.src/ts/index.ts`      | `www/js/bundle.js`
ES2015      | `www.src/es/index.js`      | `www/js/bundle.js`
SCSS        | `www.src/scss/styles.scss` | `www/css/bundle.css`
HTML        | `www.src/*.html`           | `www/*.html`
CSS         | `www.src/css/**/*`         | `www/css/**/*`
Images      | `www.src/img/**/*`         | `www/img/**/*`
JavaScript  | `www.src/js/**/*`          | `www/js/**/*`
Lib files   | `www.src/lib/**/*`         | `www/lib/**/*`
Vendor files| `www.src/vendor/**/*`      | `www/vendor/**/*`

The `before prepare` hook will transpile your code (and copy files when using the external structure). If an error occurs during this process, you'll be notified. If no error occurs, you should see the something that looks like this:

```text
Starting webpack bundling and transpilation phase...
(node:46414) DeprecationWarning: loaderUtils.parseQuery() received a non-string value which can be problematic, see https://github.com/webpack/loader-utils/issues/56
parseQuery() will be replaced with getOptions() in the next major version of loader-utils.
ts-loader: Using typescript@2.2.1 and /.../example-ts-ext/tsconfig.json
... webpack bundling and typescript transpilation phase complete!
Hash: 76ef6d9645fc284a7a9c
Version: webpack 2.2.1
Time: 1977ms
         Asset     Size  Chunks             Chunk Names
  js/bundle.js  22.6 kB       0  [emitted]  main
css/bundle.css  14.8 kB       0  [emitted]  main
    index.html  2.52 kB          [emitted]
  img/logo.png  21.8 kB          [emitted]
```

The output indicates that four assets were generated. (The paths are relative to your `www` folder.) The `bundle.*` files are transformed from your ES2015+/TypeScript or SCSS files. The other files are files that were copied (this example was from an project using the external structure).

> **Note**: If you are using the sibling project structure, an `after prepare` step will execute. This step removes duplicate files in the resulting platform build artifacts so that your original source files aren't needlessly copied to your app bundles.

> **Note**: If you've copied in your own `index.html` file, you'll need to update your `index.html` file to reference `js/bundle.js` and `css/bundle.css` instead of your original entry files.

### Debug vs Release

The plugin watches for a `--release` switch on the command line; if it is detected the following occurs:

* Minification is turned on
* Sourcemaps are turned off

If you need to change this behavior, you can override it by copying `webpack.config.js` in your project root to `webpack.release.config.js` and making the desired changes.

### Modifying the configuration files

If you wish to modify `webpack.config.js`, `webpack.release.config.js`, or `tsconfig.json`, you can. The plugin will not attempt to override their contents, and it won't attempt to overwrite the files on a reinstall. If you need to reset these configuration files, delete them and reinstall the plugin.

# Built-in Webpack Loaders

The `webpack.config.js` files come with some useful loaders:

file pattern        | loader       | example
-------------------:|:------------:|:-----------------------------
*.json; *.json5     | json5-loader | `import pkg from "../../package.json";`
*.html; *.txt       | raw-loader   | `import template from "../templates/list-item.html";`
*.png; *.jpg; *.svg | file-loader  | `import icon from "../img/icon.svg";`

If a file pattern you need to import isn't matched with a loader, you can specify the loader directly:

```javascript
import xml from "raw-loader!../../config.xml";
```

# Built-in asset copying

When in the "external" operating mode, the following assets will be copied from `www.src` to `www`:

```
*.html
img/**/*
css/**.*
js/**.*
vendor/**.*
lib/**.*
html/**/*
```

# Built-in Module Resolution

The default configurations add the following module resolution paths (relative to project root):

```
(www|src.www)/(es|ts)/lib
(www|src.www)/(es|ts)/vendor
(www|src.www)/lib
(www|src.www)/vendor
node_modules
```

# Built-in Aliases

The default configurations add the following aliases, which may be useful in resolving your app's modules:

Alias            | Path
----------------:|:--------------------------------------
`$LIB`           | `(www|src.www)/lib`
`Lib`            | `(www|src.www)/lib`
`$VENDOR`        | `(www|src.www)/vendor`
`Vendor`         | `(www|src.www)/vendor`

# License

Apache 2.0.

# Changes

* 0.3.0 completely eliminates the similarity to `cordova-plugin-webpack-transpiler`; the template doesn't need to be nearly as flexible as the plugin, and so this reduces a lot of moving parts. Everything is still configurable, but no `webpack.common.js` is needed anymore, and the `afterPrepare` script has been removed.