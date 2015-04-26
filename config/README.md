## Config

### TOC

* [koa.js](#koajs)                     <=== config, passport
* [config.js](#configjs)
* [passport.js](passportjs)
* [router.js](routerjs)

---

### [koa.js](koa.js)

```js
module.exports = function (app, config, passport) {
  if (!config.app.keys) { throw new Error("Please add session secret key in the config file!"); }
  app.keys = config.app.keys;

  if (config.app.env !== "test") {
    app.use(logger());
  }

  app.use(errorHandler());

  if (config.app.env === "production") {
    app.use(serve(path.join(config.app.root, "build", "public"), SERVE_OPTIONS, STATIC_FILES_MAP));
      // serve: config.app.root/build/public
  }

  app.use(session({                    // session <= key & store(storage location) 
    key: "koareactfullexample.sid",
    store: new MongoStore({ url: config.mongo.url }),
  }));

  /**
    > show dbs
    koareactfullexample_dev  0.078GB
    > use koareactfullexample_dev      // switched to db koareactfullexample_dev
    > show collections
    counts
    sessions                           // here is the session  
    system.indexes
    users
   */

  app.use(bodyParser());
  app.use(passport.initialize());
  app.use(passport.session());

  app.use(function *(next) {
    this.render = views(config.app.root + "/src/views", {
      map: { html: "swig" },
      cache : config.app.env === "development" ? "memory" : false,
    });                                // cache it or not 
    yield next;
  });

  app.use(compress());
  app.use(responseTime());
};
```


### [config.js](config.js)

```js
var specific = {
  development: {...},
  test: {...},
  production: {
    app: {
      port: process.env.PORT || 3000,
      name: "Koa React Gulp Mongoose Mocha",
    },
    mongo: {
      url: "mongodb://localhost/koareactfullexample",      // mongodb scheme, need no port number
    },
  },                                                       // always use `,', instead of  'nothing'
};

module.exports = _.merge(base, specific[env]);
```


### [passport.js](passport.js)

```js
                    /**
                     * in `src/models/user.js'
                     *     mongoose.model("User", UserSchema); // create a model
                     */
// use the model
var User = require("mongoose").model("User");

var serialize = function (user, done) {
  done(null, user._id);
};

var deserialize = function (id, done) {
  User.findById(id, done);
};

module.exports = function (passport, config) {
  passport.serializeUser(serialize);
  passport.deserializeUser(deserialize);
  passport.use(new LocalStrategy(authenticator.localUser));
};
```

### [router.js](router.js)

```js
var router = require("koa-router");
                                       // using controllers to help routing
var countController = require("../src/controllers/count");
var indexController = require("../src/controllers/index");
var authController = require("../src/controllers/auth");

var secured = function *(next) {
  if (this.isAuthenticated()) {
    yield next;                        // pass the authentication
  } else {
    this.status = 401;                 // failed and return 401 
  }
};

                                       // export for app's using
module.exports = function (app, passport) {
  // register functions
  app.use(router(app));

  app.get("/", function *() {
    yield indexController.index.apply(this);
  });

  app.get("/auth", authController.getCurrentUser);
  app.post("/auth", authController.signIn);

  app.all("/signout", authController.signOut);
  app.post("/signup", authController.createUser);

  // secured routes
  app.get("/value", secured, countController.getCount);
  app.get("/inc", secured, countController.increment);
  app.get("/dec", secured, countController.decrement);
};
```
