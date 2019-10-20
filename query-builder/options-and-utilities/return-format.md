# Return Format

`returnFormat` refers to the transformation your executed query makes \(if any\) before being returned to you.  You can choose one of three return formats:

* `"array"`
* `"query"`
* A custom function

By default, qb returns an array of structs as the result of your query. This is the same as specifying `array` as your `returnFormat`:

{% code-tabs %}
{% code-tabs-item title="config/ColdBox.cfc" %}
```javascript
moduleSettings = {
    "qb": {
        "returnFormat": "array"
    }
};
```
{% endcode-tabs-item %}
{% endcode-tabs %}

You can get the original query object that CFML generates by setting the `returnFormat` to `query`:

{% code-tabs %}
{% code-tabs-item title="config/ColdBox.cfc" %}
```javascript
moduleSettings = {
    "qb": {
        "returnFormat": "query"
    }
};
```
{% endcode-tabs-item %}
{% endcode-tabs %}

If you want complete control over your return result, you can provide a function as a `returnFormat`. The results of the function will be returned as the results of the builder.

{% code-tabs %}
{% code-tabs-item title="config/ColdBox.cfc" %}
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
{% endcode-tabs-item %}
{% endcode-tabs %}

