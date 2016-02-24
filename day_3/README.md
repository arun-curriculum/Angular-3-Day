# Angular Concluded

## Flexible Routing with UI Router
- UI Router gives us the ability to set up our application using states instead of directly relying on routes.
- $routeProvider only gives us the ability to create top-level routes, which could limit the ability to create a truly modular app.
- The documentation can be found [here](https://github.com/angular-ui/ui-router).

## How it Works
- Instead of setting up views, you set up states:

```javascript
app.config(function($stateProvider, $urlRouterProvider) {
	$stateProvider
		.state("home", {
			url: "/home",
			templateUrl: "templates/home.html"
		});
});
```

- This works alongside a main HTML file that includes the view:

##### index.html

```html
<div ui-view></div>
```

- home.html can also contain a view, which will render any child states:

##### home.html

```html
<h1>This is the home page</h1>

<div ui-view></div>
```

- You can insert any child view into the `ui-view` directive via the child state configuration:

```javascript
app.config(function($stateProvider, $urlRouterProvider) {
	$stateProvider
		.state("home", {
			url: "/home",
			templateUrl: "templates/home.html"
		})
	
		.state("home.widget", {
			url: "/widget",
			templateUrl: "templates/home.widget.html"
		});
});
```

- We can now change our home.html template slightly to link to our child state:

##### home.html

```html
<h1>This is the home page</h1>

<div ui-view></div>

<div>
	<a ui-sref="home.widget">Show Widget</a>
</div>
```

## Modularity with UI Router
- This is all well and good so far, but nesting views only gets us one step closer towards a fully modular app.
- What we really need is the ability to break up our views into modular pieces that are each controlled by their own template and controller.
- What we are essentially trying to accomplish is similar to inserting multiple directives on a single page, but instead tying it all to the route and state.
- Long story short, Angular UI Router gives us the functionality to create named views, which allows us to accomplish just that.
- Let's take an example:

##### index.html

```html
<div ui-view=""></div>
```

##### home.html

```html
<h1>Home Page</h1>

<div ui-view="header">
</div>

<div ui-view="content">
</div>

<div ui-view="footer">
</div>
```

##### app.js

```javascript
app.config(function($stateProvider, $urlRouterProvider) {

    $urlRouterProvider.otherwise("/home");

    $stateProvider
        .state("home", {
            url: "/home",
            views: {
                "": {
                    templateUrl: "templates/home.html"
                },
                "header@home": {
                    templateUrl: "templates/header.html",
                    controller: function($scope) {
                        alert("Header controller");
                    }
                },
                "content@home": {
                    templateUrl: "templates/content.html",
                    controller: function($scope) {
                        alert("Content controller");
                    }
                },
                "footer@home": {
                    templateUrl: "templates/footer.html",
                    controller: function($scope) {
                        alert("Footer controller");
                    }
                }
            }
        });
        
});
```

- In this example we have a main index.html that includes a home template.
- The home template includes three other templates with their individual html file and controller.
- The `@` symbol maps the various components to their proper state.

## Spotify Search App Lab Part 1
- In this lab we will be working on a song search application using the Spotify API.
- The front end has already been completed for you [here](spotify_song_search/).
- In this first part we will set up a new Angular application using UI Router as our routing system.
- Your job is to set this up and then use Angular to allow users to enter a search query and display the results on the list.html template.
- Documentation for the Spotify API can be found [here](https://developer.spotify.com/web-api/).