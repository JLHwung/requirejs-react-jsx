# requirejs-react-jsx

A RequireJS plugin for compiling React JSX files. Will use [Babel](https://babeljs.io/docs/usage/jsx/) when compiling using `r.js`, and will use [Babel/browser](https://babeljs.io/docs/usage/browser/) when running in the browser in development. This allows us to support multiple bundles in `r.js` and exclude the `Babel` from all of them since we're requiring it dynamically and not explicitly.

# Install

```sh
$ bower install https://github.com/JLHwung/requirejs-react-jsx.git --save
```

If you're not using [bower](http://bower.io/search/) to manage your dependencies (you should), you can just download the [jsx.js](jsx.js) file manually.

Since we're using [Babel](https://babeljs.io/docs/usage/jsx/) for the build step while running in a node process, and not in the browser, you will need to install that also:

```sh
$ npm install babel-core --save
 ```

# Usage

### Setup

`app.js`

```js
define(function(require){

  var React = require('react');

  function App() {
    this.AppView = React.createClass({
      render: function () {
        return (
          <div>
            <p>Hello, React!</p>
          </div>
        );
      }
    });
  }

  App.prototype.init = function () {
    React.render(<this.AppView />, document.body);
  };

  return App;

});
```

`main.js`

```js
require.config({
  paths: {
    "react": "bower_components/react/react-with-addons",
    "BabelBrowser": "node_modules/babel-core/browser",
    "jsx": "bower_components/requirejs-react-jsx/jsx",
    "text": "bower_components/requirejs-text/text"
  },

  shim : {
    "react": {
      "exports": "React"
    },
  },

  config: {
    jsx: {
      fileExtension: ".jsx",
      usePragma: false
    }
  }
});

require(['jsx!app'], function(App){

  var app = new App();
  app.init();

});
```

### Building

Call with `$ node bower_components/r.js/dist/r.js -o build.js`

In your [r.js](https://github.com/jrburke/r.js/) `build.js` config:

```
// add `optimize=none` to skip script optimization (useful during debugging).

({
  appDir: "./",
  baseUrl: "./",
  dir: "./compiled",
  mainConfigFile: "./main.js",

  optimize: "uglify2",
  skipDirOptimize: true,
  generateSourceMaps: true,
  findNestedDependencies: true,
  preserveLicenseComments: false,

  onBuildWrite: function (moduleName, path, singleContents) {
    return singleContents.replace(/jsx!/g, '');
  },

  modules: [
    {
      name: "main",
      exclude: ['jsx']
    }
  ]
})
```

# License

[MIT](LICENSE)
