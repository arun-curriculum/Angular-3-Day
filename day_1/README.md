#Introduction to Angular JS
- Angular is a great framework that allows you to write front end code in a sort of MVC pattern.
- The library wraps in many utility functions such as AJAX and data binding that are a part of the core functionality.
- Angular also gives us a clean format for writing single-page applications (SPAs).

##Install Software
- [Install NodeJS](https://nodejs.org/)
- [Install simplehttpserver](https://github.com/andrewpthorp/simple-http-server/)

##App Setup
- Angular apps have some basic configuration that we need to do before they can be run.
- We will need the main script located [here](https://angularjs.org/).
- Because Angular uses AJAX to load in its views, it's best to run any Angular app via a server.
- We will be using simplehttpserver that we downloaded earlier to accomplish this.

##Data Binding
- One of the core aspects of Angular is data binding.
- Angular is a two-way binding system. This means that data that is inputted on the HTML side is also immediately available to JavaScript.
- For example, form fields that are filled out are accessible without any additional code. How did we used to do it?
- Data binding is accomplished through the `$scope` variable, which is a common theme throughout any Angular application.
- Let's see an example:

```html
<input type="text" ng-model="userEntry" />

<div>
	{{ userEntry }}
</div>
```

- Because of this we don't have to do tons of extra work grabbing things from the DOM and adding event listeners and such.
- All data within views is accessible via `$scope`.

##Angular Modules
- Modules are like a container for different parts of the application.
- They are like the main hub that links up controllers, services, filters, directives, and more.
- This allows your code to be reusable throughout your application.
- Modules can be loaded in any order, or even in parallel, because the modules delay execution.
- They are declared on the HTML side by the `ng-app` directive.

#####Modules can be attached to elements, even the `body` or `html` tags

HTML

```html
<div ng-app="myApp">
</div>
```

JS

```javascript
var myAppModule = angular.module("myApp", []);
```

- Now this functionality is hooked up to Angular, and can be manipulated using controllers, services, factories, etc.

##Controllers
- Controllers are sort of the brain of your application.
- They are responsible for the "business logic" of the app.
- These are what interact with that `$scope` variable we saw earlier.
- Let's see an example:

```javascript
app.controller("userListController", function($scope) {
	$scope.users = [
		{
			firstname: "Arun",
			lastname: "Sood",
			age: 28,
			username: "arsood"
		},
		
		{
			firstname: "John",
			lastname: "Doe",
			age: 34,
			username: "jdoe"
		}
	];
});
```
- To sync this with the HTML we simply can add this to the `body` tag:

```html
<body ng-controller="userListController">
```

- You can attach controllers to individual elements, but bear in mind that the `$scope` variable would only pertain to functionality inside of these elements and nothing else.

##Built-In Directives
- Angular comes packed with great features that allow us to extend the functionality of our applications.
- The first directive we will look at is `ng-repeat`.
- This allows us to loop through data and display it on the page.
- Here is an example using a table row:

```html
<tr ng-repeat="todo in todos">{{todo.todoText}}</tr>
```

##Todo List Lab
- We will be building a todo list using Angular.
- The front end is already done for you [here](todo_html/).
- Use each of the concepts we learned to make your app work.
- **Bonus:** When each todo is clicked, create a strikethrough on the todo text.
- **Bonus:** Persist your data using localStorage.

##Using $http for AJAX Calls
- So far we have seen how we can use `$scope` variables to bring data into the HTML view.
- Normally though, these values aren't hard-coded. We usually use AJAX to pull dynamic values.
- This can be accomplished through the `$http` service built into Angular.
- To use the $http service we will have to "inject" this dependency into the controller:

```javascript
app.controller("userListController", function($scope, $http) {
	$http.get("url here")
		.success(function(users) {
			$scope.users = users;
		})
		
		.error(function() { //Error here });
});
```

##Wine List Lab Part 1
- In this lab we will be using Angular to send a GET request to pull a list of wines.
- Set up a new Angular project and try to make a GET request to `http://daretodiscover.herokuapp.com/wines`.
- Use the front end provided [here](wine_manager_html/) to display the wine data on the page.

##$routeProvider
- Routes allow you to add multiple page functionality to your app.
- Views are triggered via the location hash.
- Routes are set up on the application configuration:

```javascript
app.config(["$routeProvider", function($routeProvider) {
	$routeProvider
		.when("/wines", {
			templateUrl: "templates/wine-list.html",
			controller: "wineCtrl"
		})
		.otherwise({
			redirectTo: "/wines"
		});
}]);
```

- In order to use this new ngRoute module however it has to be one of our dependencies in our module setup:

```javascript
var app = angular.module("wineApp", ["ngRoute"]);
```

##Custom Directives
- We have already seen how directives enhance the functionality of the page.
- If we want to create custom functionality that can be reused throughout the app we can register our own custom directive.
- Directives can be declared via element, attribute, or class.
- The Angular team recommends using either the element or attribute form for intuitiveness.

HTML

```html
<my-todos></my-todos>
```

JS

```javascript
app.directive("myTodos", function() {
	return {
		templateUrl: "templates/todos.html"
	}
});
```

- You can split your templates into multiple files, essentially creating a partial.
- templateUrl can be a string as in the above, but it can also be a function that gets access to the element and any related attributes.
- Imagine we had an attributes we wanted to use:

HTML

```html
<my-todos type="awesome"></my-todos>
```

JS

```javascript
app.directive("myTodos", function() {
	return {
		templateUrl: function(elem, attr) {
			console.log(elem);
			console.log(attr.type);
			return "templates/todos.html"
		}
	}
});
```

#####Restricting directive types
- By default Angular directives are restricted to element and attribute types.
- If you want to use a directive that is triggered by a class name you will have to use the `restrict` option.
- Restrict supports three types - A, E, and C for attribute, element, and class name respectively.
- Let's see an example where we want the directive to only be available as a class name:

HTML

```html
<div class="my-todos" type="awesome"></div>
```

JS

```javascript
app.directive("myTodos", function() {
	return {
		restrict: "C",
		templateUrl: function(elem, attr) {
			console.log(elem);
			console.log(attr.type);
			return "templates/todos.html"
		}
	}
});
```

##Wine List Lab Part 2
- In this lab we will add routing functionality to the app using `$routeProvider`.
- We will also be refactoring some of our reusable code into custom directives.

##Filters
- Filters allow you to alter $scope data to transform it in some way.
- Think of it like we are passing the data through some filter before it is shown to the user or used in the JavaScript.
- Let's look at an example that will take numbers entered into an input box and multiply them by 2:

HTML

```html
<input type="text" ng-model="numbers" />
{{numbers | multiply}}
```

JS

```javascript
app.filter("multiply", function() {
	return function(input) {
		if (input) {
			var num = parseInt(input);

			return num * 2;
		} else {
			return "";
		}
	}
});
```