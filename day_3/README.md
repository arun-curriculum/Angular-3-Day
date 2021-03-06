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
- Use named views in your setup.
- Documentation for the Spotify API can be found [here](https://developer.spotify.com/web-api/).

## Passing Parameters to States
- Passing parameters to states will be important to make sure the required information gets transmitted through the URL.
- The nice thing about using states is that any child states automatically have access to these parameters too.
- To use this functionality we will need the $stateParams dependency:

##### app.js

```javascript
$stateProvider
        .state("home.hello", {
            url: "/hello/:greeting",
            views: {
                "greeting": {
                    templateUrl: "templates/hello.html",
                    controller: function($scope, $stateParams) {
                        $scope.greeting = $stateParams.greeting;
                    }
                }
            }
        });
```

##### hello.html

```html
<h3>Hey there!</h3>

<h4>
    {{greeting}}
</h4>
```

## Spotify Search App Lab Part 2
- In this part we will be creating the functionality for song.html.
- You will need to use the Spotify API to pull information about the song.
- Set up the "Related Artists" section as a named view with its own controller.
- "Related Artists" should pull the first 4 related artists for a specific artist based on their ID provided in the URL parameters.

## Introduction to Web Sockets
- One of the most powerful uses for Node is its ability to handle seamless "real-time" experiences.
- Sockets are a way for a browser and server to communicate without the standard request-response cycle.
- Chat clients, real-time data feeds, and operational dashboards are some examples of where sockets have been used effectively.

## How it Basically Works
- A client makes an initial request out to the server and a "handshake" is created - AKA a connection has been established.
- This "handshake" is given a unique socket with a unique ID.
- Essentially this request never completes and remains open for the duration of the session.
- Every further request-response simulation is done via a manifestation of a JavaScript event.
- In a perfect world this is how things would always operate with sockets but certain factors such as browser incompatibility and more can interfere with a proper handshake. As a result, a more brute-force approach of "polling" may be required.

## Socket.io
- Socket.io is a library that essentially manages browser capabilities to connect a client with a server through web sockets in the most ideal way possible.
- It can switch between polling and sockets automatically and basically automate the handshake process.
- Socket.io works on the client and the server side to achieve seamless interaction.

## Socket-Based Chat Mechanism
- We will be building a chat application in Node using web sockets.
- We will use Express JS as the application framework.

##### The Server Setup
- For this project we will need to import the Express and Socket.io modules into the project:

```javascript
var express = require('express');
var app = express();
var http = require('http').Server(app);
var io = require('socket.io')(http);
```

- You will need to use this new variable `http` for our listen method:

```javascript
http.listen(3000);
```

- To handle the initial handshake, Socket.io registers a `connection` event:

```javascript
io.on('connection', function(socket) { });
```

- This is now a unique socket for this specific connection.
- Any events to this socket can easily be detected and dealt with:

```javascript
socket.on('event', function(params) { });
```

- Any event can also be "emitted" from the socket if necessary:

```javascript
io.emit('event', params);
```

- You can also emit events to all sockets connected except for yours by using `broadcast`:

```javascript
socket.broadcast.emit('event', params);
```

##### The Client Setup
- The client will also use Socket.io to handle the handshake and any further events.
- For this part we will be using a [3rd party module](https://github.com/btford/angular-socket-io).
- The first thing that will be needed is to create the handshake with the server:

```javascript
var socket = io.connect("server_url or blank for current server");
```

- The client can also detect and respond to events:

```javascript
socket.on('event', function(params) { });
```

- The client can also "emit" events:

```javascript
socket.emit('event', params);
```

## In-Class Lab: Build the Chat
- In this lab we will be working to create a real-time chat application.
- The front end is already done for you [here](socket_chat/).
- You will be working to create the chat functionality using web sockets.
- A working public server can be found at: http://arunchatserver.herokuapp.com/
- **Bonus:** Use your knowledge to change the page title when a new chat is received.