React is amazing, but setting up a React project sucks. Here is how [Tyler McGinnis](https://twitter.com/tylermcginnis33) does it in his [free React Fundamentals Course](http://courses.reactjsprogram.com/courses/reactjsfundamentals).

# Getting a React app started

Initialize npm
```
npm init
```

Install React and React-DOM
```
npm install --save react react-dom
```

Install Webpack, HTML Webpack Plugin, Webpack Dev Server, Babel Core, Babel Loader, Babel Preset React
```
npm install --save-dev html-webpack-plugin webpack webpack-dev-server babel-{core,loader} babel-preset-react
```

Make app folder
```
mkdir app && cd app
```

Make index.html file (should be located within the app directory)
```
touch index.html
```

Inside the index.html file, set up HTML basic structure and create div with an ID of app within the body.
```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>React App</title>
  </head>
  <body>
    <div id="app"></div>
  </body>
</html>
```

Create an empty index.js file in the same app directory.
```
touch index.js
```

Create webpack config file in the root directory. Webpack tells your app how to put together a finished app in a dist folder using all of the files you have created.
```
cd ..
touch webpack.config.js
```

Inside webpack.config.js, insert the following code.
```
module.exports = {
  entry: [
    './app/index.js'
  ],
  output: {
    path: __dirname + '/dist',
    filename: 'index_bundle.js'
  },
  module: {
    loaders: [
      // loaders tell webpack what transformations to run on your code (like JSX to JavaScript)
      {test: /\.js$/, exclude: /node_modules/, loader: 'babel-loader'}
      // this says to choose all JS files except for those in node modules and use babel to transpile JSX to JavaScript. Babel also does many other transformations.
    ]
  }
}
```

Create a .babelrc and include the following code. This will tell Babel to convert JSX to regular JavaScript.
```
touch .babelrc
```
```
{
  "presets": [
    "react"
  ]
}
```

Now we need to include HTML Webpack Plugin. At the top of the webpack.config.js file, insert the following code. Also add the plugin to the bottom of webpack.config.js. The final file should look like this:

```
var HtmlWebpackPlugin = require('html-webpack-plugin');
var HtmlWebpackPluginConfig = new HtmlWebpackPlugin({
  template: __dirname + '/app/index.html', //the file we want to transfer to dist
  filename: 'index.html',
  inject: 'body'
});

module.exports = {
  entry: [
    './app/index.js'
  ],
  output: {
    path: __dirname + '/dist',
    filename: 'index_bundle.js'
  },
  module: {
    loaders: [
      {test: /\.js$/, exclude: /node_modules/, loader: 'babel-loader'}
    ]
  },
  plugins: [HtmlWebpackPluginConfig]
}
```

In package.json, delete the default script and insert the following:
```
"scripts": {
  "production": "webpack -p"
}
```
This allows you to run `npm run production` from the command line which goes through our Webpack options, minifies evrything, and makes everything ready for production. It will also create the dist folder the first time you run it.

In index.js, require React and React-DOM. Also create your first React component.
```
var React = require('react');
var ReactDOM = require('react-dom');

var HelloWorld = React.createClass({
  render: function () {
    return (
      <div> Hello, World! </div>
    )
  }
})
```

Then, tell React where to render the component using React-DOM.
```
ReactDOM.render(
  <HelloWorld />,
  document.getElementById('app')
)
```

Create another script called start to run the webpack dev server.
```
"scripts": {
  "production": "webpack -p",
  "start": "webpack-dev-server"
}
```

Finally, a Hello World app!

# Next Steps

## Set up React Router
Install React Router
```
npm install --save react-router
```

Create a config folder inside app. Then create a routes.js file inside config.
```
mkdir app/config
touch app/config/routes.js
```

Inside app/config/routes.js, insert the following code:
```
var React = require('react');
var ReactRouter = require('react-router');
var Router = ReactRouter.Router;
var Route = ReactRouter.Route;
var IndexRoute = ReactRouter.IndexRoute;
```

Also include the components that you will create.
```
var Main = require('../components/Main');
var Home = require('../components/Home');
```

Build the routes.
```
var routes = (
  <Router>
    <Route path='/' component={Main}>
      <Route path='/home' component={Home} />
    </Route>
  </Router>
)

module.exports = routes;
```

Then build out the components you've included.
```
mkdir app/components
touch app/components/Main.js
touch app/components/Home.js
```

Inside the new components, include the following code:
```
var React = require('react');

var Home = React.createClass({
  render: function () {
    return (
      <div> Hello from Home </div>
    )
  }
});

module.exports = Home;
```

```
var React = require('react');

var Main = React.createClass({
  render: function () {
    return (
      <div>
        Hello from Main
        {this.props.children}
      </div>
    )
  }
});

module.exports = Main;
```

In index.js, remove the HelloWorld component and tell ReactDOM to render the routes. The file should look like this at the end.

```
var React = require('react');
var ReactDOM = require('react-dom');
var routes = require('./config/routes');

ReactDOM.render(
  routes,
  document.getElementById('app')
);
```

Check everything is working by running `npm run start` and checking endpoints at '/' and '/home'

Next, make '/home' your IndexRoute and the set up other routes with the <Route> tag. routes.js should now look like this:
```
var routes = (
  <Router>
    <Route path='/' component={Main}>
      <IndexRoute component={Home} />
    </Route>
  </Router>
)
```

Finally, add hashHistory (can also use browser or memory history). This is because the app currently doesn't have a server. The final routes.js should look like this:
```
var React = require('react');
var ReactRouter = require('react-router');
var Router = ReactRouter.Router;
var Route = ReactRouter.Route;
var IndexRoute = ReactRouter.IndexRoute;
var hashHistory = ReactRouter.hashHistory;
var Main = require('../components/Main');
var Home = require('../components/Home');

var routes = (
  <Router history={hashHistory}>
    <Route path='/' component={Main}>
      <IndexRoute component={Home} />
    </Route>
  </Router>
)

module.exports = routes;
```
