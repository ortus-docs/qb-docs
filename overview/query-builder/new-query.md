# New Query

A query builder is a stateful, transient object.
That means that if you want to execute two different queries, you need two separate instances of `QueryBuilder`.

```javascript
// This will cause you pain and grief...

var user = query.from( "users" )
  .where( "username", rc.username )
  .first();
  
var posts = query.from( "posts" ).get();
// This will error because `username` is not a column in `posts`.
```

An easy way to get a new query builder is to use the `newQuery` method available on the builder.

```javascript
// This will cause you pain and grief...

var user = query.from( "users" )
  .where( "username", rc.username )
  .first();
  
var posts = query.newQuery().from( "posts" ).get();
// This will work as we expect it to.
```

The `newQuery` method will keep the current grammar, return format, and utils attached to the called query builder.
