
# x0

Zero-config React development environment and static site generator
[![Build Status][build-badge]][build]

```sh
npm install -g @compositor/x0
```

[build-badge]: https://img.shields.io/travis/c8r/x0/master.svg?style=flat-square
[build]: https://travis-ci.org/c8r/x0

![screen-demo](docs/demo.gif)

## Features

- Zero-config
- Hot-loading development environment
- Works with virtually any React component\*
- No confusing APIs
- Renders static HTML
- Renders JS bundles
- Works with CSS-in-JS libraries like [styled-components][sc] and [glamorous][glamorous]
- Automatic file system based routing
- Support for async data fetching

\* Custom [webpack configuration](#webpack) is required for components that rely on webpack-based features


## Isolated development environment

```sh
x0 components
```

Options:

```
-o --open       Open dev server in default browser
-p --port       Custom port for dev server
-t --template   Path to custom HTML template
--webpack       Path to custom webpack configuration
```

```sh
x0 components -op 8080
```


## Static Build

Export static HTML and client-side bundle

```sh
x0 build components
```

Export static HTML without bundle

```sh
x0 build components --static
```

Options

```
-d --out-dir    Output directory (default dist)
-s --static     Output static HTML without JS bundle
-t --template   Path to custom HTML template
--webpack       Path to custom webpack configuration
```


## Fetching Data

Use the async `getInitialProps` static method to fetch data for static rendering.
This method was inspired by [Next.js][nextjs].

```jsx
const Index = props => (
  <h1>Hello {props.data}</h1>
)

Index.getInitialProps = async () => {
  const fetch = require('isomorphic-fetch')
  const res = await fetch('http://example.com/data')
  const data = await res.json()

  return { data }
}
```

## Custom App

A custom `App` component can be provided by including an `_app.js` file.
The `App` component uses the [render props][render-prop] pattern to provide additional state and props to its child routes.

```jsx
// example _app.js
import React from 'react'

export default class extends React.Component {
  state = {
    count: 0
  }

  update = fn => this.setState(fn)

  render () {
    const { render, routes } = this.props

    return render({
      ...this.state,
      decrement: () => this.update(s => ({ count: s.count - 1 })),
      increment: () => this.update(s => ({ count: s.count + 1 }))
    })
  }
}
```

### Layouts

The `App` component can also be used to provide a common layout for all routes.

```jsx
// example _app.js
import React from 'react'
import Nav from '../components/Nav'
import Header from '../components/Header'
import Footer from '../components/Footer'

export default class extends React.Component {
  render () {
    const {
      render,
      routes
    } = this.props

    const route = routes.find(route => route.path === props.location.pathname)

    return (
      <React.Fragment>
        <Nav />
        <Header
          route={route}
        />
        {render()}
        <Footer />
      </React.Fragment>
    )
  }
}
```

## CSS-in-JS

x0 supports server-side rendering for [styled-components][sc] with zero configuration.
To enable CSS rendering for static output, ensure that `styled-components` is installed as a dependency in your `package.json`

```json
"dependencies": {
  "styled-components": "^3.2.6"
}
```

## Configuration

Default options can be set in the `x0` field in `package.json`.

```json
"x0": {
  "static": true,
  "outDir": "site",
  "title": "Hello",
}
```

## Head content

Head elements such as `<title>`, `<meta>`, and `<style>` can be configured with the `x0` field in `package.json`.

```json
"x0": {
  "title": "My Site",
  "meta": [
    { "name": "twitter:card", "content": "summary" }
    { "name": "twitter:image", "content": "kitten.png" }
  ],
  "links": [
    {
      "rel": "stylesheet",
      "href": "https://fonts.googleapis.com/css?family=Roboto"
    }
  ]
}
```

## Custom HTML Template

A custom HTML template can be passed as the `template` option.

```json
"x0": {
  "template": "./html.js"
}
```

```js
// example template
module.exports = ({
  html,
  css,
  scripts,
  title,
  meta = [],
  links = [],
  static: isStatic
}) => `<!DOCTYPE html>
<head>
  <title>{title}</title>
  ${css}
</head>
<div id=root>${html}</div>
${scripts}
`
```

### Routing

x0 creates routes based on the file system, using [react-router][react-router].
To set the base URL for static builds, use the `basename` option.

```json
"x0": {
  "basename": "/my-site"
}
```

### webpack

Webpack configuration files named `webpack.config.js` will automatically be merged with the built-in configuration, using [webpack-merge][webpack-merge].
To use a custom filename, pass the file path to the `--webpack` flag.

```js
// webpack.config.js example
module.exports = {
  module: {
    rules: [
      { test: /\.txt$/, loader: 'raw-loader' }
    ]
  }
}
```

See the [example](examples/webpack-config).

#### Related

- [Create React App](https://github.com/facebookincubator/create-react-app)
- [Next.js][nextjs]
- [Gatsby][gatsby]
- [React-Static][react-static]

---

[Made by Compositor](https://compositor.io/)
|
[MIT License](LICENSE.md)

[nextjs]: https://github.com/zeit/next.js
[react-router]: https://github.com/ReactTraining/react-router
[sc]: https://github.com/styled-components/styled-components
[glamorous]: https://github.com/paypal/glamorous
[glamor]: https://github.com/threepointone/glamor
[fela]: https://github.com/rofrischmann/fela
[gatsby]: https://github.com/gatsbyjs/gatsby
[react-static]: https://github.com/nozzle/react-static
[react-loadable]: https://github.com/thejameskyle/react-loadable
[webpack-merge]: https://github.com/survivejs/webpack-merge
