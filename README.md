# backbone.collectionsubset

Create sub-collections of other collections and keep them in sync.

```coffee
tasks = new TaskCollection

# Create a subset of our main collection
today = tasks.subcollection
  filter: (task) -> task.isToday()
today.url = '/tasks/today'

# Create a new task in the main collection...
tasks.create id: 200, today: true

# ...and it's automatically pushed to all subsets
today.get(200) is tasks.get(200) # true

# Model changes are pushed upwards
today.get(200).destroy()

# Create models in subsets and they are pushed to the parent collection
today.create id: 201
today.get(201) is tasks.get(2001) # true

# And they will update on fetch
tasks.fetch()

# When subsets are fetched, all the models are pushed to the parent collection
today.fetch()
```

## Getting Started

Download the [production version][min] or the [development version][max].

[min]: https://raw.github.com/anthonyshort/backbone.collectionsubset/master/dist/backbone.collectionsubset.min.js
[max]: https://raw.github.com/anthonyshort/backbone.collectionsubset/master/dist/backbone.collectionsubset.js

In your web page:

```html
<script src="dist/backbone.collectionsubset.min.js"></script>
```

Now you have two methods for creating subsets (or subcollections).

### Collection::subcollection

The easiest way to create a subset is to use `Collection::subcollection`

```js
var TaskCollection = Backbone.Collection.extend();
var tasks = new TaskCollection;
var today = tasks.subcollection({
  filter: function(task) {
    return task.isToday();
  }
});
```

`today` will be an instance of `tasks.constructor`, in this case, `TaskCollection`.

### Backbone.CollectionSubset

If you need access to the subset object itself (you might want to modify the filter) you can create a subset directly.

```js
var allTasks = new Backbone.Collection;
var subset = new Backbone.CollectionSubset({
  parent: allTasks,
  filter: function(task) {
    return task.isToday();
  }
});

var todayTasks = subset.child;

subset.setFilter(function(model){
  return task.isYesterday();
});
```

## Documentation

Depending on how you create the subset you have different interface. Creating a subcollection via `Backbone.Collection::subcollection` you will only have
access to the child collection and the Backbone.CollectionSubset instance is completely transparent. However, you may need some extra flexibility so you can
also create `Backbone.CollectionSubset` instances manually

```coffee
subset = new Backbone.CollectionSubet parent: tasks
```

vs.

```coffee
new_collection = tasks.subcollection
  filter: (model) -> model.isToday()
```

### Options

* `refresh` - Whether to refresh the collection from the parent immediately. Defaults to `true`
* `triggers` - Set which model attribute changes to watch for. This is important for performance. Defaults to `null`
* `filter` - The default filter. By default lets all models pass
* `child` - The collection to use for the child. This isn't required and will be automatically created. Defaults to `null`
* `parent` - *required* The collection to base the subset on. Must be a Backbone.Collection.

### Methods

If you create a Backbone.CollectionSubset instance directly you will have access to these methods.

`setParent`

Set the parent collection. Will unbind any events on the collection that the subset current has and setup all the events for syncing with the child collection

`setChild`

Same as the previous option, but with the child collection.

`setFilter`

Set the filter used on the parent collection. This does not automatically refresh the child collection.

`refresh`

Refreshes the child collection with models from the parent. Clears out the child collection and refilters the parent. Triggers a 'refresh' event on the child.

`dispose`

Clean up the subset. Removes the child collection completely and removes all events from the parent child. Triggers a `dispose` event on the subset.

## Todo

* Add url option to `subcollection` that will prepend the parent collection url

## Contributing
In lieu of a formal styleguide, take care to maintain the existing coding style. Add unit tests for any new or changed functionality. Lint and test your code using [grunt](https://github.com/cowboy/grunt).

_Also, please don't edit files in the "dist" subdirectory as they are generated via grunt. You'll find source code in the "lib" subdirectory!_

## Release History
_(Nothing yet)_

## License
Copyright (c) 2012 Anthony Short
Licensed under the MIT license.