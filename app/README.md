## React

```text
koa-react-full-example/app
├── app.jsx                                      // main entry
├── application
│   └── index.jsx
├── components
│   ├── counter.jsx                              // react elements go here
│   └── navbar.jsx
├── composition
│   └── full-height.jsx
├── layouts                                      // webpage layouts   
│   ├── anonymous.jsx
│   ├── authenticated.jsx
│   └── index.jsx
├── less
│   ├── main.less
│   └── transitions.less
├── mixins
│   └── authentication.jsx
├── pages                                        // pages
│   ├── index.jsx
│   ├── notfound.jsx
│   ├── null.jsx
│   ├── signin.jsx
│   ├── signout.jsx
│   └── signup.jsx
├── README.md
└── stores
    ├── auth.jsx
    └── counter.jsx
```


### [app.jsx](app.jsx)

```js
"use strict";
import React, { PropTypes } from "react";
import TransitionGroup from "react/lib/ReactCSSTransitionGroup";
import Router, { Route, DefaultRoute, NotFoundRoute, Redirect } from "react-router";


// components
import Navbar from "./components/navbar";
import { Authenticated as Layout, Anonymous as AnonymousLayout } from "./layouts"; /*
                                                                                        // layouts/index.jsx
                                                                                        export {
                                                                                          Anonymous,
                                                                                          Authenticated,
                                                                                        };
                                                                                    */

import Application from "./application";

// pages
import IndexPage from "./pages/index";
import NotFoundPage from "./pages/notfound";
import NullPage from "./pages/null";
import SignInPage from "./pages/signin";
import SignUpPage from "./pages/signup";
import SignOut from "./pages/signout";

// the container
const container = document.getElementById("content");

// the routes
const routes = (
  <Route handler={Application}>                                           // application, main route
    <Route name="anonymous" path="/auth" handler={AnonymousLayout}>       // login, route  
      <Route name="sign-in" path="signin" handler={SignInPage} />
      <Route name="sign-up" path="signup" handler={SignUpPage} />
      <Route name="sign-out" path="signout" handler={SignOut} />
      <NotFoundRoute handler={NotFoundPage} />
    </Route>
    <Route name="home" path="/" handler={Layout}>                         // user, route  
      <DefaultRoute name="index" handler={IndexPage} />
      <Route name="null-page" path="null" handler={NullPage} />
      <Route name="profile" path="profile" handler={NullPage} />
      <NotFoundRoute handler={NotFoundPage} />
    </Route>
  </Route>
);

Router.run(routes, Router.HashLocation, (Handler) => {                    // es6, ()=>{}, cool~  
  React.render(<Handler/>, container);                                    // render this part  
});
```


### [application/index.jsx](application/index.jsx)

```js
"use strict";
import AuthStore from "../stores/auth";                                   // what for?  

require("../less/main.less");                                             // require css  

const App = React.createClass({
  displayName: "App",

  contextTypes: {
    router: PropTypes.func
  },

  getInitialState() {
    return {
      hasLoaded: false,
    };
  },

  componentWillMount() {
    AuthStore.init();
  },

  componentDidMount() {
    AuthStore.addChangeListener(this.updateLoading);
  },

  componentWillUnmount() {
    AuthStore.removeChangeListener(this.updateLoading);
  },

  updateLoading() {
    AuthStore.removeChangeListener(this.updateLoading);
    this.setState({
      hasLoaded: true,
    });
  },

  render() {
    let key = this.context.router.getCurrentPath();
    return (
      <div>
        <Navbar brand="React Koa Gulp Mongoose Mocha Demo" />             // navbar, brand (hero)
        <div className="transition-crop main-container" style={{ "minHeight": this.props.height}}>
            <TransitionGroup transitionName="transition">                 // transition group?  
              <RouteHandler key={key} />
            </TransitionGroup>
          </div>
      </div>
    );
  }
});

const FullHeightApp = makeFullHeightComponent(App, () => {
  let height = window.innerHeight;
  let navbarHeight = document.getElementsByClassName("main-container")[0].getBoundingClientRect().top;
  let footerHeight = document.getElementsByClassName("footer")[0].offsetHeight;
  return height - navbarHeight - footerHeight;
});

export default FullHeightApp;
```

### [components/counter.jsx](components/counter.jsx)

```js
const get = (url, cb) => {                                 // get~, request.get(url).set(headerkey, headerval).end(callback function)
  request.get(url)
  .set("Content-Type", "application/json")
  .end(cb);
}

const Counter =  React.createClass({                       // Counter, a React Class  
  displayName: "Counter",                                  // with a displayName 

  getInitialState() {                                      // init the counter 
    return { count : this.props.initialCount || 0 };
  },

  componentWillMount() {                                   // some listeners 
    get("/value", (err, res) => {                          // get! 
      this.setState({ count: res.body.count });
    });
  },

  onClickInc(event) {...},                                 // inc     
  onClickDec(event) {...},                                 // dec 
  render() {                                               // render, then it will appear on the screen 
    return (                                               // render via creating some HTML elements     
      <div>
        <h3>Counter</h3>
        <div className="counter">                          // .counter, for css, maybe
          Count
          <Badge>{this.state.count}</Badge>                // counter badge, inc, dec events (this.onClickInc/onClickDec) 
          <Button bsStyle="success" onClick={this.onClickInc}>Increment</Button>
          <Button bsStyle="danger" onClick={this.onClickDec}>Decrement</Button> 
        </div>                                             // button style: "success" & "danger"
      </div>
    );
  }
});

export default Counter;                                    // export default Counter. 
```

### [components/navbar.jsx](components/navbar.jsx)

```js
import { Navbar, Nav, Glyphicon } from "react-bootstrap";  // react-bootstrap

import { NavItemLink } from "react-router-bootstrap";

import AuthStore from "../stores/auth";                    // stores/auth 

const AppNavbar = React.createClass({
  displayName: "AppNavbar",
  getInitialState() {
    return {
      user: AuthStore.getUser(),                           // AuthStore.getUser()
    };
  },
  componentWillMount() {...},
  componentDidMount() {...},
  componentWillUnmount() {...},
  updateUser() {...},
  renderBrand() {
    return (<Link to="index">{this.props.brand}</Link>);   // render brand
  },

  renderNavLinks() {
    if (this.state.user) {
      return (
        <Nav right eventKey={0}>
          <NavItemLink eventKey={1} to="profile">          // icon
            <Glyphicon glyph="user" /> {this.state.user.username} 
          </NavItemLink>
          <NavItemLink to="sign-out">
            <Glyphicon glyph="off" /> Sign out
          </NavItemLink>
        </Nav>
      );
    }
    return (
      <Nav right eventKey={0}>
        <NavItemLink eventKey={1} to="sign-up">
           <Glyphicon glyph="user" /> Sign up
        </NavItemLink>
      </Nav>
    );
  },

  render() {
    return (
      <Navbar brand={this.renderBrand()} inverse fixedTop toggleNavKey={this.state.user ? 0 : undefined}>
        {this.renderNavLinks()}
      </Navbar>
    );
  }
});

export default AppNavbar;                                  // AppNavbar     
```

### [composition/full-height.jsx](composition/full-height.jsx)

```js
export default function makeFullHeightComponent(Component, heightCalculationFunc) {
  if (!heightCalculationFunc) {
    throw new Error("You must Provided a height calculation function");
  }
  return React.createClass({
    displayName: "FullHeightWrapper",

    propTypes: {...},

    getInitialState() {
      return {
        height: 0,
      };
    },

    getComponent() {
      return this.refs.childComponent;
    },

    componentDidMount() {                        // eventListener
      window.addEventListener("resize", this.calculateHeight);
      this.calculateHeight();
    },

    componentWillUnmount() {                     // eventListener
      window.removeEventListener("resize", this.calculateHeight);
    },

    calculateHeight() {
      this.setState({
        height: heightCalculationFunc(),         // the function servers here
      });
    },

    render() {                                   // spread-magic in es6
      return (<Component {...this.props} ref="childComponent" height={this.state.height} />);
    },
  });
}
```

### [layouts/index.jsx](layouts/index.jsx)

```js
import Anonymous from "./anonymous";                       // import two layouts
import Authenticated from "./authenticated";

export {
  Anonymous,
  Authenticated,
};                                                         // export two layouts 
```


### [layouts/authenticated.jsx](layouts/authenticated.jsx)

```js
import AuthStore from "../stores/auth";
import Authentication from "../mixins/authentication";     // what for? todo

const MainLayout = React.createClass({
  displayName: "MainLayout",

  mixins: [Authentication],                                // using the mixins here, then what is a mixin? 

  render() {
    return (
      <div>
      <div className="container">                         // container  
        <Row>
          <Col md={2}>
            <h3>Links</h3>
            <Nav bsStyle="pills" stacked>
              <NavItemLink to="index">Index</NavItemLink>
              <NavItemLink to="null-page">Null</NavItemLink>
            </Nav>
          </Col>
          <Col md={10} className="well">
            <RouteHandler />
          </Col>
        </Row>
      </div>
      </div>
    );
  }
});

export default MainLayout;                                 // export default MainLayout
```


### [mixins/authentication.jsx](mixins/authentication.jsx)

```js
import SignIn from "../pages/signin";
import AuthStore from "../stores/auth";

const Authentication = {
  statics: {
    willTransitionTo(transition) {
      if (!AuthStore.isLoggedIn()) {
        SignIn.attemptedTransition = transition;
        transition.redirect("sign-in");
      }
    }
  }
};

export default Authentication;
```


### [pages/index.jsx](pages/index.jsx)

```js
import Counter from "../components/counter";               // import counter

const Index = React.createClass({
  displayName: "IndexPage",

  render() {
    return (
      <div>
        <h2>Index - Super Counter</h2>
        <Counter />                                        // using it 
      </div>
    );
  }                                                        // return(render) it
});

export default Index;                                      // export it 
```

### [stores/auth.jsx](stores/auth.jsx)

```js
var _user = null;
var _changeListeners  = [];
var _initCalled = false;

var URLS = {                                               // pack URLS     
  AUTH: "/auth",
  SIGN_UP: "/signup",
  SIGN_OUT: "/signout"
};

var AuthStore = {
  init: function () {
    if(_initCalled) {
      return;
    }
    _initCalled = true;
    this.fetchUser();
  },
  fetchUser: function () {
    request.get(URLS.AUTH)                                 // RESTful API, request.get().set(...).end(cb)
      .set("Accept", "application/json")
      .set("Content-Type", "application/json")
      .end(function (err, res) {
        if (!err && res.body && res.body.user) {
          _user = parseUser(res.body.user);
        }
        AuthStore.notifyChange();
      });
  },
  signIn: function (username, password, done) {
    _postAndHandleParseUser(URLS.AUTH, username, password, done);
  },
  signUp: function (username, password, done) {
    _postAndHandleParseUser(URLS.SIGN_UP  , username, password, done);
  },
  signOut: function (done) {
    _user = null;
    request.get(URLS.SIGN_OUT)
      .set("Accept", "application/json")
      .set("Content-Type", "application/json")
      .end(function (err, res) {
        if (!err) {
          AuthStore.notifyChange();
        }
        if (done) {
          done(null, res);
        }
      });
  },
  isLoggedIn: function () {
    return _user !== null;
  },
  getUser: function () {
    return _user;
  },
  notifyChange: function() {
    _changeListeners.forEach(function (listener) {
      listener();
    });
  },
  addChangeListener: function (listener) {
    _changeListeners.push(listener);
  },
  removeChangeListener: function (listener) {
    _changeListeners = _changeListeners.filter(function (l) {
      return listener !== l;
    });
  },
};

function _postAndHandleParseUser (url, username, password, done) {
  request.post(url)
    .set("Accept", "application/json")
    .set("Content-Type", "application/json")
    .send({ username: username, password: password })
    .end(function (err, res) {
      if (!err && res.body && res.body.user) {
        _user = parseUser(res.body.user);
        AuthStore.notifyChange();
      }
      if (done) {
        done(err, _user);
      }
    });
}

function parseUser (user) {
  return {
    id: user.id,
    username: user.username,
  };
}

module.exports = AuthStore;
```


