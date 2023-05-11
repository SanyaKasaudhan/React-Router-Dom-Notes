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
