# React-Router-Dom-Notes
You need to be using React >= 16.8 in order to use any of these hooks!

useHistory

useLocation

useParams

useRouteMatch

# useHistory
The useHistory hook gives you access to the history instance that you may use to navigate.

```javascript
import { useHistory } from "react-router-dom";

function HomeButton() {
  let history = useHistory();

  function handleClick() {
    history.push("/home");
  }

  return (
    <button type="button" onClick={handleClick}>
      Go home
    </button>
  );
}
```

# useLocation
The useLocation hook returns the location object that represents the current URL. You can think about it like a useState that returns a new location whenever the URL changes.This could be really useful e.g. in a situation where you would like to trigger a new “page view” event using your web analytics tool whenever a new page loads, as in the following example:

```javascript
import React from "react";
import ReactDOM from "react-dom";
import {
  BrowserRouter as Router,
  Switch,
  useLocation
} from "react-router-dom";

function usePageViews() {
  let location = useLocation();
  React.useEffect(() => {
    ga.send(["pageview", location.pathname]);
  }, [location]);
}

function App() {
  usePageViews();
  return <Switch>...</Switch>;
}

ReactDOM.render(
  <Router>
    <App />
  </Router>,
  node
);
```

# useParams
useParams returns an object of key/value pairs of URL parameters. Use it to access match.params of the current <Route>
  
\```javascript
import React from "react";
import ReactDOM from "react-dom";
import {
  BrowserRouter as Router,
  Switch,
  Route,
  useParams
} from "react-router-dom";

function BlogPost() {
  let { slug } = useParams();
  return <div>Now showing post {slug}</div>;
}

ReactDOM.render(
  <Router>
    <Switch>
      <Route exact path="/">
        <HomePage />
      </Route>
      <Route path="/blog/:slug">
        <BlogPost />
      </Route>
    </Switch>
  </Router>,
  node
);
```
  
# useRouteMatch
The useRouteMatch hook attempts to match the current URL in the same way that a <Route> would. It’s mostly useful for getting access to the match data without actually rendering a <Route>.Now, instead ofimport { Route } from "react-router-dom";

```javascript
function BlogPost() {
  return (
    <Route
      path="/blog/:slug"
      render={({ match }) => {
        // Do whatever you want with the match...
        return <div />;
      }}
    />
  );
}
```
you can justimport { useRouteMatch } from "react-router-dom";

```javascript
function BlogPost() {
  let match = useRouteMatch("/blog/:slug");

  // Do whatever you want with the match...
  return <div />;
}
```
The useRouteMatch hook either:
takes no argument and returns the match object of the current <Route>
takes a single argument, which is identical to props argument of matchPath. It can be either a pathname as a string (like the example above) or an object with the matching props that Route accepts, like this:
```javascript
const match = useRouteMatch({
  path: "/BLOG/:slug/",
  strict: true,
  sensitive: true
});
```

# <Link>
Provides declarative, accessible navigation around your application.

An object that can have any of the following properties:
pathname: A string representing the path to link to.
search: A string representation of query parameters.
hash: A hash to put in the URL, e.g. #a-hash.
state: State to persist to the location.
```javascript
<Link
  to={{
    pathname: "/courses",
    search: "?sort=name",
    hash: "#the-hash",
    state: { fromDashboard: true }
  }}
/>
```

# <NavLink>
A special version of the <Link> that will add styling attributes to the rendered element when it matches the current URL.

```javascript
   <NavLink to="/about">About</NavLink>
```

# <Redirect>
Rendering a <Redirect> will navigate to a new location. The new location will override the current location in the history stack, like server-side redirects (HTTP 3xx) do.
```javascript
<Route exact path="/">
  {loggedIn ? <Redirect to="/dashboard" /> : <PublicHomePage />}
</Route>
```
 # <Route>
The Route component is perhaps the most important component in React Router to understand and learn to use well. Its most basic responsibility is to render some UI when its path matches the current URL.Consider the following code:import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter as Router, Route } from "react-router-dom";

ReactDOM.render(
  <Router>
    <div>
      <Route exact path="/">
        <Home />
      </Route>
      <Route path="/news">
        <NewsFeed />
      </Route>
    </div>
  </Router>,
  node
);

# Route render methods
The recommended method of rendering something with a <Route> is to use children elements, as shown above. There are, however, a few other methods you can use to render something with a <Route>. These are provided mostly for supporting apps that were built with earlier versions of the router before hooks were introduced.
1. <Route component>
      
2. <Route render>
      
3. <Route children> function

Route props
All three render methods will be passed the same three route props
  
match
  
location
  
history
  
 # component
A React component to render only when the location matches. It will be rendered with route props.
  
  ```javascript
  import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter as Router, Route } from "react-router-dom";

// All route props (match, location and history) are available to User
function User(props) {
  return <h1>Hello {props.match.params.username}!</h1>;
}

ReactDOM.render(
  <Router>
    <Route path="/user/:username" component={User} />
  </Router>,
  node
);
```
When you use component (instead of render or children, below) the router uses React.createElement to create a new React element from the given component. That means if you provide an inline function to the component prop, you would create a new component every render. This results in the existing component unmounting and the new component mounting instead of just updating the existing component. When using an inline function for inline rendering, use the render or the children prop (below).
      
# render: func
This allows for convenient inline rendering and wrapping without the undesired remounting explained above.Instead of having a new React element created for you using the component prop, you can pass in a function to be called when the location matches. The render prop function has access to all the same route props (match, location and history) as the component render prop.

```javascript
import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter as Router, Route } from "react-router-dom";

// convenient inline rendering
ReactDOM.render(
  <Router>
    <Route path="/home" render={() => <div>Home</div>} />
  </Router>,
  node
);

// wrapping/composing
// You can spread routeProps to make them available to your rendered Component
function FadingRoute({ component: Component, ...rest }) {
  return (
    <Route
      {...rest}
      render={routeProps => (
        <FadeIn>
          <Component {...routeProps} />
        </FadeIn>
      )}
    />
  );
}

ReactDOM.render(
  <Router>
    <FadingRoute path="/cool" component={Something} />
  </Router>,
  node
);
```

Warning: <Route component> takes precedence over <Route render> so don’t use both in the same <Route>.
children: func
Sometimes you need to render whether the path matches the location or not. In these cases, you can use the function children prop. It works exactly like render except that it gets called whether there is a match or not.The children render prop receives all the same route props as the component and render methods, except when a route fails to match the URL, then match is null. This allows you to dynamically adjust your UI based on whether or not the route matches. Here we’re adding an active class if the route matches.

```javascript
import React from "react";
import ReactDOM from "react-dom";
import {
  BrowserRouter as Router,
  Link,
  Route
} from "react-router-dom";

function ListItemLink({ to, ...rest }) {
  return (
    <Route
      path={to}
      children={({ match }) => (
        <li className={match ? "active" : ""}>
          <Link to={to} {...rest} />
        </li>
      )}
    />
  );
}

ReactDOM.render(
  <Router>
    <ul>
      <ListItemLink to="/somewhere" />
      <ListItemLink to="/somewhere-else" />
    </ul>
  </Router>,
  node
);
This could also be useful for animations:<Route
  children={({ match, ...rest }) => (
    {/* Animate will always render, so you can use lifecycles
        to animate its child in and out */}
    <Animate>
      {match && <Something {...rest}/>}
    </Animate>
  )}
/>

```
Warning: <Route children> takes precedence over both <Route component> and <Route render> so don’t use more than one in the same <Route>.
# path: string | string[]
Any valid URL path or array of paths that path-to-regexp@^1.7.0 understands.

```javascript
   <Route path="/users/:id">
  <User />
</Route>
<Route path={["/users/:id", "/profile/:id"]}>
  <User />
</Route>
Routes without a path always match.
exact: bool
When true, will only match if the path matches the location.pathname exactly.<Route exact path="/one">
  <About />
</Route>
path	location.pathname	exact	matches?
/one	/one/two	true	no
/one	/one/two	false	yes

```
      
 # <Switch>
Renders the first child <Route> or <Redirect> that matches the location.How is this different than just using a bunch of <Route>s?<Switch> is unique in that it renders a route exclusively. In contrast, every <Route> that matches the location renders inclusively. Consider these routes:

```javascript
   import { Route } from "react-router";

let routes = (
  <div>
    <Route path="/about">
      <About />
    </Route>
    <Route path="/:user">
      <User />
    </Route>
    <Route>
      <NoMatch />
    </Route>
  </div>
);

```
If the URL is /about, then <About>, <User>, and <NoMatch> will all render because they all match the path. This is by design, allowing us to compose <Route>s into our apps in many ways, like sidebars and breadcrumbs, bootstrap tabs, etc.Occasionally, however, we want to pick only one <Route> to render. If we’re at /about we don’t want to also match /:user (or show our “404” page). Here’s how to do it with Switch:

```javascript
import { Route, Switch } from "react-router";
let routes = (
  <Switch>
    <Route exact path="/">
      <Home />
    </Route>
    <Route path="/about">
      <About />
    </Route>
    <Route path="/:user">
      <User />
    </Route>
    <Route>
      <NoMatch />
    </Route>
  </Switch>
```
      
     
);
Now, if we’re at /about, <Switch> will start looking for a matching <Route>. <Route path="/about"/> will match and <Switch> will stop looking for matches and render <About>. Similarly, if we’re at /michael then <User> will render.
