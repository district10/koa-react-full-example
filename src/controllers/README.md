## controllers

## TOC

* [auth.js](#authjs)
* [count.js](#countjs)

---

### [auth.js](auth.js)

```js
var passport = require("koa-passport");

exports.signIn = function *() {
  var ctx = this;
  yield* passport.authenticate("local", function*(err, user, info) { // delegate
    if (err) {
      throw err;
    }
    if (user === false) {
      ctx.status = 401;
    } else {
      yield ctx.login(user);
      ctx.body = { user: user };
    }
  }).call(this);
};

exports.getCurrentUser = function *() {
  if (this.passport.user) {
    this.body = { user: this.passport.user };
  }
  this.status = 200;
};

exports.signOut = function *() {
  this.logout();
  this.session = null;
  this.status = 204;
};

exports.createUser = function *() {
  if (!this.request.body) {
    this.throw("The body is empty", 400);
  }

  if (!this.request.body.username) {
    this.throw("Missing username", 400);
  }
  if (!this.request.body.password) {
    this.throw("Missing password", 400);
  }

  var User = require("mongoose").model("User");            // model

  try {                                                    // model item 
    var user = new User({ username: this.request.body.username, password: this.request.body.password });
    user = yield user.save();                              // save item
    yield this.login(user);
  } catch (err) {
    this.throw(err);
  }

  this.status = 200;
  this.body = { user: this.passport.user };
};
```


### [count.js](count.js)

```js
var Count = mongoose.model("Count");             // using model: count

exports.getCount = function *() {
  var count = yield Count.findOne().exec();
  if (!count) {
    count = new Count();
  }
  this.body = { count: count.value };
};

exports.increment = function *() {
  var count = yield Count.findOne().exec();      // get the count (actually its one count, and everybody use it)
  if (!count) {
    count = new Count();
  }
  ++count.value;                                 // do something to this count   

  yield count.save();                            // save it back
  this.body = { count: count.value };
};

exports.decrement = function *() {...};
```
