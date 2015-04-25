## [Mongoose Models v4.0.2](http://mongoosejs.com/docs/models.html)

### What is Schema?

> Models are fancy constructors compiled from our Schema definitions.
> Instances of these models represent documents which can be saved 
> and retreived from our database. 
> All document **creation** and **retreival** from the database is **handled by these models**.

Steps:

* compiling a model: `mongoose.model()`
* constructing documents: `new Model({key-val}), .save(cb) to save`
* querying: `model.find({query-key-val})`
* removing
* updating

```js
// schema
var schema = new mongoose.Schema({ name: 'string', size: 'string' });
// model
var Tank = mongoose.model('Tank', schema);                 // create a model, with a schema
// item
var small = new Tank({ size: 'small' });                   // using it to new something
// save the item
small.save(function (err) {                                // save the newed item   
  if (err) return handleError(err);
  // saved!
})

// or save an item directly
Tank.create({ size: 'small' }, function (err, small) {     // or create one directly
  if (err) return handleError(err);
  // saved!
})
// query
Tank.find({ size: 'small' }).where('createdDate').gt(oneYearAgo).exec(callback);
// removing
// updating
```

### count.js

```js
"use strict";
var mongoose = require("mongoose");
var Schema = mongoose.Schema;

/**
 * Schema
 */
var CountSchema = new Schema({
  value: { type: Number, default: 0 },
  updated: { type: Date, default: Date.now },
});

CountSchema.pre("save", function (next) {
    this.updated = new Date();
    next();
});

mongoose.model("Count", CountSchema);
```


### user.js
```js
"use strict";
var bcrypt = require("../../lib/bcrypt-thunk"); // version that supports yields
var mongoose = require("mongoose");
var Schema = mongoose.Schema;
var co = require("co");

var UserSchema = new Schema({
  username: { type: String, required: true, unique: true, lowercase: true },
  password: { type: String, required: true },
}, {
  toJSON : {
    transform: function (doc, ret, options) {
      delete ret.password;
    },
  },
});

/**
 * Middlewares
 */
UserSchema.pre("save", function (done) {
  // only hash the password if it has been modified (or is new)
  if (!this.isModified("password")) {
    return done();
  }

  co.wrap(function*() {
    try {
      var salt = yield bcrypt.genSalt();
      var hash = yield bcrypt.hash(this.password, salt);
      this.password = hash;
      done();
    }
    catch (err) {
      done(err);
    }
  }).call(this).then(done);
});

/**
 * Methods
 */
UserSchema.methods.comparePassword = function *(candidatePassword) {
  return yield bcrypt.compare(candidatePassword, this.password);
};

/**
 * Statics
 */

UserSchema.statics.passwordMatches = function *(username, password) {
  var user = yield this.findOne({ username: username.toLowerCase() }).exec();
  if (!user) {
    throw new Error("User not found");
  }

  if (yield user.comparePassword(password)) {
    return user;
  }

  throw new Error("Password does not match");
};

// Model creation
mongoose.model("User", UserSchema);
```
