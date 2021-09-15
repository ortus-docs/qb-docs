# Return Format

`returnFormat` refers to the transformation your executed query makes \(if any\) before being returned to you. You can choose one of three return formats:

* `"array"`
* `"query"`
* A custom function

By default, qb returns an array of structs as the result of your query. This is the same as specifying `array` as your `returnFormat`:

{% code title="config/ColdBox.cfc" %}
```javascript
moduleSettings = {
    "qb": {
        "returnFormat": "array"
    }
};
```
{% endcode %}

You can get the original query object that CFML generates by setting the `returnFormat` to `query`:

{% code title="config/ColdBox.cfc" %}
```javascript
moduleSettings = {
    "qb": {
        "returnFormat": "query"
    }
};
```
{% endcode %}

This setting can be overridden on a per-instance basis by calling `setReturnFormat()`:

{% code title="setReturnFormat" %}
```javascript
var qb = wirebox.getInstance( "QueryBuilder@qb" );

qb
   .setReturnFormat( 'query' )
   .from( 'users' )
   .get()
```
{% endcode %}

If you want complete control over your return result, you can provide a function as a `returnFormat`. The results of the function will be returned as the results of the builder.

{% code title="config/ColdBox.cfc" %}
```javascript
moduleSettings = {
    "qb": {
        "returnFormat": function( q ) {
            return application.wirebox.getInstance(
                "name" = "Collection",
                "initArguments" = { "collection": q }
            );
        }
    }
};
```
{% endcode %}

