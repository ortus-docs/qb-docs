# Conditionals

## Basic `if` and `else`

If you store the builder object in a variable, you can use `if` and `else` statements like you would expect.

```text
var builder = builder.from( "posts" );
if ( rc.recent ) {
    builder.orderBy( "published_date", "desc" );
}
var results = builder.get();
```

This works, but breaks chainability. A better way is to use the `when` helper method.

## `when`

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| condition | boolean | true |  | The condition to switch on. |
| onTrue | Closure | true |  | The callback to execute if the condition is true.  It is passed the `builder` object as the only parameter. |
| onFalse | Closure | false | function\( q \) { return q; } | The callback to execute if the conditions is false.  It is passed the `builder` object as the only parameter. |

We can rewrite the above query like so:

```text
var results = builder.from( "posts" )
    .when( rc.recent, function( q ) {
        q.orderBy( "published_date", "desc" );
    } )
    .get();
```

Nice. We keep chainability this way and reduce the number of temporary variables we need.

