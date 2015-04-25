### NOTES

* [server.js](server.md)
* [src/models](src/models/README.md)
* [config](config/README.md)
* [app/](app/README.md)

### Tree

```text
koa-react-full-example
├── app
│   ├── app.jsx
│   ├── application
│   │   └── index.jsx
│   ├── components
│   │   ├── counter.jsx
│   │   └── navbar.jsx
│   ├── composition
│   │   └── full-height.jsx
│   ├── layouts
│   │   ├── anonymous.jsx
│   │   ├── authenticated.jsx
│   │   └── index.jsx
│   ├── less
│   │   ├── main.less
│   │   └── transitions.less
│   ├── mixins
│   │   └── authentication.jsx
│   ├── pages
│   │   ├── index.jsx
│   │   ├── notfound.jsx
│   │   ├── null.jsx
│   │   ├── signin.jsx
│   │   ├── signout.jsx
│   │   └── signup.jsx
│   └── stores
│       ├── auth.jsx
│       └── counter.jsx
├── build
│   └── stats.json
├── config
│   ├── config.js
│   ├── koa.js
│   ├── passport.js
│   └── routes.js
├── HISTORY.md
├── lib
│   ├── authenticator.js
│   └── bcrypt-thunk.js
├── LICENSE
├── package.json
├── README.md
├── register-babel.js
├── server.js
├── src
│   ├── controllers
│   │   ├── auth.js
│   │   ├── count.js
│   │   └── index.js
│   ├── models
│   │   ├── count.js
│   │   └── user.js
│   └── views
│       └── basic.html                           // the basic html file, TITLE goes here
├── test
│   ├── middlewares
│   │   ├── authenticator.js
│   │   └── database.js
│   ├── test-auth.js
│   ├── test-bcrypt-thunk.js
│   ├── test-count.js
│   ├── test-error.js
│   └── test-index.js
├── webpack
│   ├── strategies
│   │   ├── development.js
│   │   ├── index.js
│   │   ├── optimize.js
│   │   ├── style.js
│   │   └── version.js
│   └── webpack.config.js
└── webpack.config.js
```


### [register-babel.js](register-babel.js)

```js
require("babel/register")({
  ignore: /node_modules/,
  optional: ["es7.objectRestSpread", "runtime"]
});
```
