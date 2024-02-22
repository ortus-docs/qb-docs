# When / Conditionals

If you store the builder object in a variable, you can use `if` and `else` statements like you would expect.

{% code title="QueryBuilder" %}
```javascript
var q = query.from( "posts" );
if ( someFlag ) {
    q.orderBy( "published_date", "desc" );
}
```
{% endcode %}

This works, but breaks chainability. To keep chainability you can use the `when` helper method.

## `when`

| Name           | Type     | Required | Default                     | Description                                                                                                   |
| -------------- | -------- | -------- | --------------------------- | ------------------------------------------------------------------------------------------------------------- |
| condition      | boolean  | true     |                             | The condition to switch on.                                                                                   |
| onTrue         | Function | true     |                             | The callback to execute if the condition is true.  It is passed the `builder` object as the only parameter.   |
| onFalse        | Function | false    | function( q ) { return q; } | The callback to execute if the conditions is false.  It is passed the `builder` object as the only parameter. |
| withoutScoping | boolean  | false    | `false`                     | Flag to turn off the automatic scoping of where clauses during the callback.                                  |

The `when` helper is used to allow conditional statements when defining queries without using if statements and having to store temporary variables.

{% code title="QueryBuilder" %}
```javascript
query.from( "posts" )
    .when( someFlag, function( q ) {
        q.orderBy( "published_date", "desc" );
    } )
    .get();
```
{% endcode %}

You can pass a third argument to be called in the `else` case.

{% code title="QueryBuilder" %}
```javascript
query.from( "posts" )
    .when(
        someFlag,
        function( q ) {
            q.orderBy( "published_date", "desc" );
        },
        function( q ) {
            q.orderBy( "modified_date", "desc" );
        }
    );
```
{% endcode %}

`when` callbacks are automatically scoped and grouped.  That means that if a where clause is added inside the callback with an `OR` combinator the clauses will automatically be grouped (have parenthesis put around them.)  You can disable this feature by passing `withoutScoping = true` to the `when` callback.

```javascript
qb.from( "users" )
    .where( "active", 1 )
    .when( len( url.q ), function( q ) {
        q.where( "username", "LIKE", q & "%" )
            .orWhere( "email", "LIKE", q & "%" );   
    } );
```

```sql
SELECT *
FROM "users"
WHERE "active" = ?
    AND (
        "username" = ?
        OR "email" = ?
    )
```
