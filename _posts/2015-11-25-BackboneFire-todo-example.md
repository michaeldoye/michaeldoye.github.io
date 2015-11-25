---
layout: post
tags:
  - javascript
  - backbone.js
  - firebase
title: Simple todo App with Backbone and Firebase
published: false
---






[Firebase](https://www.firebase.com) is a fantastically handy realtime database, in which you store your data in JSON format, this tutorial aims to provide a very simple example [todo app](https://backbonefire.firebaseapp.com/) which you can easily extend upon to build something amazing in realtime.


## First steps

To begin with, we need to include some dependencies in our HTML, these will allow us to use BackboneFire:

```
<!-- We need Backbone, Underscore and jQuery -->
<script src="path/to/jquery.js"></script>
<script src="path/to/underscore.js"></script>
<script src="path/to/backbone.js"></script>

<!--Include Firebase -->
<script src="https://cdn.firebase.com/js/client/2.3.2/firebase.js"></script>

<!-- Include BackboneFire -->
<script src="https://cdn.firebase.com/libs/backbonefire/0.5.1/backbonefire.min.js"></script>
```
## Getting started with Firebase

For this tutorial we will use a `Backbone.Firebase.collection` to sync data with the Firebase database. Use your Firebase URL as the `url` property for the collection. We will also need to create 2 views for this app - one for individual todos and one for the whole app.


```
// First, create a basic model
var MyTodo = Backbone.Model.extend({
  defaults: {
    title: "New Todo"
  }
});

// Create a Firebase Collection, use your Firebase URL as the url property
var MyTodoCollection = Backbone.Firebase.Collection.extend({
  model: MyTodo,
  url: "https://[YOUR-FIREBASE-APP].firebaseio.com"
});
```

## How to listen for changes

One of the coolest things about Firebase is the realtime database, this means that when listening to changes on our model, we can re-render views as changes occur (even if the changes happen on a remote client!), we set this up using [`listenTo`](http://backbonejs.org/#Events-listenTo), which will essentially just call the render function.

```
// Individual todo item
var TodoView = Backbone.View.extend({
  tagName:  "li",
  template: _.template("<%= title %>"),
  initialize: function() {
    this.listenTo(this.model, "change", this.render);
  },
  render: function() {
    this.$el.html(this.template(this.model.toJSON()));
    return this;
  },
});
```
## How to read your data from Firebase

As soon as we initialize a `Backbone.Firebase.Collection` our data is synced with the Firebase database. (`fetch` is not required and Firebase will simply ignore `fetch` calls) If we `listenTo` when a collection changes are able to add new items to the collection in realtime.  Here we create a view for the entire application and set up the view to listen for when a new item is added to the collection, and then call the `addOne` function which will append the new item to the collection. Easy right?


```
// The view for the entire application
var MyAppView = Backbone.View.extend({
  el: $('#mytodoapp'),
  initialize: function() {
    this.list = this.$("#todo-items"); // the list to append to
    this.listenTo(this.collection, 'add', this.addOne);
  },
  addOne: function(todo) {
    var view = new TodoView({model: todo});
    this.list.append(view.render().el);
  }
});
```
If we include this HTML we will then be able to update TODOs in realtime :)

```
<div id="mytodoapp">
  <ul id="todo-items"><!-- Todos will appear here --></ul>
</div>
```

## How to write data to Firebase

At this stage our app is only half-baked as we haven't set up the ability to create new TODOs. To do this, all we need is to create a input field and a button.

```
<div id="mytodoapp">
  <ul id="todo-items"><!-- Todos will appear here --></ul>
  <input type="text" id="add-todo" />
  <button id="add-todo">Add Todo</button>
</div>
```
By using a `create` function in our main app view we are now able to add new TODOs to our database. You will need to modify the main view by adding the `createNewTodo` function:


```
// The main view for the application
var MyAppView = Backbone.View.extend({
  el: $('#mytodoapp'),
  events: {
    "click #add-todo" : "createNewTodo",
  },
  initialize: function() {
    this.list = this.$("#todo-items");
    // Input for new Todos
    this.input = this.$("#add-todo");
    this.listenTo(this.collection, 'add', this.addOne);
  },
  addOne: function(todo) {
    var view = new TodoView({model: todo});
    this.list.append(view.render().el);
  },
  createNewTodo: function(e) {
    // Ensure input is not empty
    if (!this.input.val()) { return; }
    this.collection.create({title: this.input.val()});
    this.input.val('');
  }
});
```
We can then create an initialize function which will start off the application

```
function initFirebase() {
  var collection = new MyTodoCollection();
  var app = new MyAppView({ collection: collection });
}
```
Finally, once the DOM is ready, we can start the application.

```

$(function() { initFirebase() });

```
And that is the short and sweet of it! You should now have a fully functioning **realtime** TODO app :)

<hr>

Be sure to see the [BackboneFire GitHub Repo](https://github.com/firebase/backbonefire) for further information and handy tips!

<sub>This tutorial is based off the [todo app by Firebase](https://backbonefire.firebaseapp.com/). Source and official docs can be found [here](https://www.firebase.com/docs/web/libraries/backbone/quickstart.html).</sub>