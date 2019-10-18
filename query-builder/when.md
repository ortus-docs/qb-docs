# When / Conditionals

If you store the builder object in a variable, you can use `if` and `else` statements like you would expect.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
var q = query.from( "posts" );
if ( someFlag ) {
    q.orderBy( "published_date", "desc" );
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

This works, but breaks chainability. To keep chainability you can use the `when` helper method.

## `when`

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| condition | boolean | true |  | The condition to switch on. |
| onTrue | Function | true |  | The callback to execute if the condition is true.  It is passed the `builder` object as the only parameter. |
| onFalse | Function | false | function\( q \) { return q; } | The callback to execute if the conditions is false.  It is passed the `builder` object as the only parameter. |

The `when` helper is used to allow conditional statements when defining queries without using if statements and having to store temporary variables.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "posts" )
    .when( someFlag, function( q ) {
        q.orderBy( "published_date", "desc" );
    } )
    .get();
```
{% endcode-tabs-item %}
{% endcode-tabs %}

You can pass a third argument to be called in the `else` case.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
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
{% endcode-tabs-item %}
{% endcode-tabs %}

