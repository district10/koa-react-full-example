### [Modules & Deps](modeps.md)



### NOTES

* [server.js](server.md)
* src: MVC
    * M: [models/](src/models/README.md): users, count, buildings, etc
    * V: views, basic.html(`#content`)
    * C: 
* [config/](config/README.md)
* [app/](app/README.md)


### Tree

```text
koa-react-full-example
├── app
│   ├── app.jsx                                  // router, <=== application   
│   ├── application
│   │   └── index.jsx                            // fullheightApp <=== ../composition/fullheight.jsx
│   ├── components
│   │   ├── counter.jsx
│   │   └── navbar.jsx
│   ├── composition
│   │   └── full-height.jsx                      // export makeFullHeightComponent
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
│   ├── koa.js                                   // koa        <== config, passport   
│   ├── passport.js
│   └── routes.js                                // routes     <== controllers
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

### [webpack.config.js](webpack.config.js)

```js
/* eslint no-var: 0 */
require("./register-babel");
var config = require("./webpack/webpack.config");
var result = config();
module.exports = result;
```
