# Return Format

`returnFormat` refers to the transformation qb applies to an executed query before returning it. qb includes four named return formatters:

* `"array"` — an array of structs. This is the default.
* `"query"` — the original CFML query object.
* `"none"` — no transformation. This also returns the original CFML query object.
* `"struct"` — a struct of rows keyed by a selected column.

You may also provide a custom formatter function.

Set the application-wide default with `defaultReturnFormat`:

{% code title="config/ColdBox.cfc" %}
```javascript
moduleSettings = {
    qb = {
        defaultReturnFormat = "query"
    }
};
```
{% endcode %}

## setReturnFormat

Override the format for a builder by calling `setReturnFormat`. The setting is preserved by `newQuery()` and `clone()`.

| Name    | Type               | Required | Default | Description                                      |
| ------- | ------------------ | -------- | ------- | ------------------------------------------------ |
| format  | string \| function | `true`   |         | A registered formatter name or formatter closure. |
| options | struct             | `false`  | `{}`    | Options passed to a named formatter factory.     |

{% code title="QueryBuilder" %}
```javascript
users = query
    .setReturnFormat( "query" )
    .from( "users" )
    .get();
```
{% endcode %}

The `struct` formatter requires a `columnKey` option. If more than one row has the same key, the last row wins.

{% code title="QueryBuilder" %}
```javascript
usersByUsername = query
    .setReturnFormat( "struct", { columnKey = "username" } )
    .select( [ "id", "username", "email" ] )
    .from( "users" )
    .get();
```
{% endcode %}

```javascript
{
    johndoe = {
        id = 1,
        username = "johndoe",
        email = "john@example.com"
    }
}
```

## withReturnFormat

Use `withReturnFormat` to temporarily change the formatter while a callback runs. The previous formatter is restored after the callback.

| Name         | Type               | Required | Default | Description                                      |
| ------------ | ------------------ | -------- | ------- | ------------------------------------------------ |
| returnFormat | string \| function | `true`   |         | A registered formatter name or formatter closure. |
| callback     | function           | `true`   |         | The code to run with the temporary formatter.    |
| options      | struct             | `false`  | `{}`    | Options passed to a named formatter factory.     |

```javascript
usersByUsername = query.withReturnFormat(
    "struct",
    function() {
        return query
            .select( [ "id", "username", "email" ] )
            .from( "users" )
            .get();
    },
    { columnKey = "username" }
);
```

## Custom Return Formatters

For one-off formatting, pass a closure directly to `setReturnFormat`. The closure receives the executed query.

```javascript
ids = query
    .setReturnFormat( function( q ) {
        return queryColumnData( q, "id" );
    } )
    .select( "id" )
    .from( "users" )
    .get();
```

Register reusable named formatter factories with the `returnFormatters` module setting. A factory receives the options passed to `setReturnFormat` and returns the closure that will format the query.

{% code title="config/ColdBox.cfc" %}
```javascript
moduleSettings = {
    qb = {
        returnFormatters = {
            ids = function( options ) {
                return function( q ) {
                    return queryColumnData( q, options.column );
                };
            }
        }
    }
};
```
{% endcode %}

```javascript
ids = query
    .setReturnFormat( "ids", { column = "id" } )
    .select( "id" )
    .from( "users" )
    .get();
```

A registered formatter can be:

* A closure factory.
* A component with a `toFormatter( options )` method.
* A WireBox mapping name that resolves to a component with a `toFormatter( options )` method.
* A definition struct with `factory`, `options`, `properties`, and `force` keys. `options`, `properties`, and `force` are optional.

```javascript
returnFormatters = {
    ids = {
        factory = "IdReturnFormatter",
        options = { column = "id" },
        properties = {},
        force = false
    }
};
```

The `options` in the definition are defaults and are overridden by options passed to `setReturnFormat` or `withReturnFormat`. Set `force` to `true` only when intentionally replacing an existing formatter with the same name.

## Native queryExecute Return Types

qb's return formatters replace the native `queryExecute` `returntype`, `columnkey`, and `columnKey` options. qb removes these options by default so the configured formatter always receives a CFML query object.

During development, enable `validateQueryExecuteReturnType` to throw an `InvalidQueryExecuteOption` exception when `returntype` is passed instead of silently removing it:

{% code title="config/ColdBox.cfc" %}
```javascript
moduleSettings = {
    qb = {
        validateQueryExecuteReturnType = true
    }
};
```
{% endcode %}

{% hint style="warning" %}
If you previously passed `returntype`, `columnkey`, or `columnKey` through query options, migrate that behavior to `setReturnFormat`, `withReturnFormat`, or a registered formatter.
{% endhint %}
