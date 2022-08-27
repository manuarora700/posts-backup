Hello folks, 

**React Router Dom** becomes one of the mandatory libraries to understand when you are working with ReactJS. Before a while, I came across this very common use-case of React Routing, where there are nested routes at many levels. Initially I was pretty confused, as React Router’s simple nested routing pattern couldn't work for me. I had to implement routing which was nested upto 3 levels. 

This becomes a very common use case when you are working with React App, so I thought of sharing my routing approach here. So, let's dive in.  

For this tutorial, I created a simple project with `create-react-app` and added `react-router-dom` to it using `npm`. As these are pretty basic steps, I am not including them into this article. If you want to skip this tutorial and directly check the code, you can check my [github repo](https://github.com/ms-yogi/react-nested-routes). 

So, we are going to create a simple looking dashboard, which will have one common sidebar with some page link. One of these pages will have a separate navigation bar on top to go to some more pages directly. We will also have one login page, from which we will go inside this dashboard. Pretty straightforward, right? 
*P.S. There will not be any login system or authorisation for any routes as that is out of scope for this tutorial.*

The routing will start by adding a `BrowserRouter` to the app. So inside `App.js`, wrap complete code into `BrowserRouter`.  Now, let us create a very simple login page, to get started with the app.

```javascript
import { Link } from 'react-router-dom';

const Login = (props) => {
    return (
	<>
	   <div className='login'>
		<p>Login to the app</p>
		<Link to='/home' className='button'>
		    Login
		</Link>
	   </div>
	</>
    );
};
export default Login;
```
Here, we are just creating a button to create an effect of login. This button will have a link to the `/home` page, where the remaining app is present.

Now, to move forward, we will create and define other routes for our app. In this app, we will have one single registry for defining routes. I will call it `Routes.js`.

In react router, common way to declare a route is to define a component and a path for that route. So, we will create an array of objects, where each object will represent a single route. With some basic routes, our `Routes.js` will look something like this, 
```javascript
import Home from './Home';
import Login from './Login';

const routes = [
    {
	path: '/login',
	component: Login,
    },
    {
	path: '/home',
	component: Home,
    },
];

export default routes;
```
Here, our Home component is the root component inside our app. Now, we need to add nesting from Home component. Inside home, we will have a common sidebar and one main section where we will render all the pages from that sidebar. 

Hence, our next requirement will be to add those pages as a nested route from the home component. To create these nested routes, we will create one similar array of routes, as we have created above, but inside Home component. React Router Dom have added a property called routes, to every Route component and that property will contain list of all the nested routes. Let's update our route component with some pages inside Home. 

So, our route will look something like this: 

```javascript 
import Home from './Home';
import Login from './Login';
import Page1 from './pages/Page1';
import Page2 from './pages/Page2';

const routes = [
    {
	path: '/login',
	component: Login,
    },
    {
	path: '/home',
	component: Home,
    // added nested routes
	routes: [              
	    {
        // Also note how we added /home before the 
        // actual page name just to create a complete path
		path: '/home/page1',
		component: Page1,
	    },
	    {
		path: '/home/page2',
		component: Page2,
	    },
	],
    },
];

export default routes;
```

Till now, we have created some nested routes but those are yet to be configured inside our app. So, let's add these to `App.js` inside `BrowserRouter`. 

```javascript
import { BrowserRouter, Redirect, Switch } from 'react-router-dom';
import routes from './Routes';
import RouteWithSubRoutes from './utils/RouteWithSubRoutes';

function App() {
    return (
	<BrowserRouter>
	    <Switch>
		<Redirect exact from='/' to='/login' />
		{routes.map((route, i) => (
		    <RouteWithSubRoutes key={i} {...route} />
		))}
	    </Switch>
	</BrowserRouter>
    );
}

export default App;
```
Code for `App.js` is quite simple. We have added one `BrowserRouter` and one 'Switch'. We are redirecting our app from `/` to `/login` and then load all routing. 

The map function is just iterating over the routes array, but now you can see one interesting component `RouteWithSubRoutes`. Let us understand what we are doing inside it. 

```javascript
import { Route } from 'react-router-dom';

const RouteWithSubRoutes = (route) => {
    return (
	<Route
	    path={route.path}
	    render={(props) => (
	        <route.component {...props} routes={route.routes} />.
	    )}
	/>
    );
};

export default RouteWithSubRoutes;

```

Here, we are just returning the same component but important point to note here is, we are passing all the subroutes, to that component. Hence, the parent route will always be aware of all of its subroutes. 

Alright, our routes are sorted, app component is sorted. Now, we will need to see how home component will manage all those subroutes which we have just passed to it.

Here is our Home component: 
```javascript
import { Switch, Link } from 'react-router-dom';
import RouteWithSubRoutes from './utils/RouteWithSubRoutes';

// Notice how we are passing routes here 
const Home = ({ routes }) => {
    const menu = [
	{
	    path: '/home/page1', // the url
	    name: 'Page1', // name that appear in Sidebar
	},
	{
	    path: '/home/page2',
	    name: 'Page2',
	},
    ];
    return (
	<div className='home'>
	{/* This can be treated as a sidebar component */}
	    <div className='sidebar'>
		<h2>React Nested Routes</h2>
		<ul>
		    {menu.map((menuItem) => (
			<li key={menuItem.name}>
			    <Link to={menuItem.path}>{menuItem.name}</Link>
			</li>
		    ))}
		</ul>
	    </div>

	    <Switch>
		{routes.map((route, i) => (
		    <RouteWithSubRoutes key={i} {...route} />
		))}
	    </Switch>
	</div>
    );
};

export default Home;

```

Home component is similar to any usual react component. We have created an array here just to show the sidebar and we have added links to all the pages inside that sidebar. One `Switch` is there to render the component which user selects on clicking the sidebar and again, it is using the same `RouteWithSubRoutes` component to pass on further subroutes(If any). 

One thing which is very important to notice here is routes passed down to home component like props. This way the Home will always be aware of its subcomponents and routing will never go wrong! 

You can keep nesting in more levels as required in your app with the same approach. In the [github repo](https://github.com/ms-yogi/react-nested-routes) I have also added a Navbar to one page. You can check that out. 

There is also one similar example in the official documentation of react router [here](https://reactrouter.com/web/example/route-config). 

That's all about the nested routing for React! If you follow some different approach for this, do let me know that in comments. 

And your feedback on article will always be welcomed!! 
Keep learning! 🙌