# Retrieving Results

## get

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| columns | string \| array | false |  | A shortcut parameter to retrieve only these columns overriding any columns previously set on the QueryBuilder. |
| options | struct | false | `{}` | Any additional `queryExecute` options. |

The `get` method is the most common method used for retrieving results.  It executes using the configured `QueryBuilder` and returns the results.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" ).get();
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="SQL \(MySQL\)" %}
```sql
SELECT * FROM `users`
```
{% endcode-tabs-item %}
{% endcode-tabs %}

`get` can also take a list or array of columns to use as a shortcut.  If any are passed, those columns will be used instead of any columns previously set on the `QueryBuilder`.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" ).get( [ "id", "name" ] );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="SQL \(MySQL\)" %}
```sql
SELECT `id`, `name` FROM `users`
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## first

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| options | struct | `false` | `{}` | Any additional `queryExecute` options. |

If you just need to retrieve a single row from the database table, you may use the `first` method. This method will return a single record \(a `Struct` by default\). If no row is found an empty `Struct` will be returned by default.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" ).first();
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="SQL \(MySQL\)" %}
```sql
SELECT * FROM `users` LIMIT(1)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## values

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| column | string | `true` |  | The name of the column to retrieve. |
| options | struct | `false` | `{}` | Any additional `queryExecute` options. |

If you don't even need an entire row, you may extract a single value from each record using the `values` method. The `values` method will return the column of your choosing as a simple array.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" ).values( "firstName" );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="Result" %}
```text
[ "jon", "jane", "jill", ... ]
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## value

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| column | string | `true` |  | The name of the column to retrieve. |
| defaultValue | string | `false` | \(empty string\) | The default value returned if there are no records returned for the query. |
| throwWhenNotFound | boolean | `false` | `false` | If `true`, it throws a `RecordCountException` if no records are returned from the query. |
| options | struct | `false` | `{}` | Any additional `queryExecute` options. |

This method is similar to `values` except it only returns a single, simple value.  Where `values` calls `get` under the hood, this method calls `first`.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" ).value( "firstName" );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="Result" %}
```text
"jon"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

If no records are returned from the query, one of two things will happen.  If the `throwWhenNotFound` boolean is set to `true`, a `RecordCountException` will be thrown.  Otherwise the `defaultValue` provided to the method will be returned.

## chunk

| Name | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| max | numeric |  | The number of results to return in each chunk.  |
| callback | Function |  | The function that will be called with each chunk. |
| options | struct | `{}` | Any additional `queryExecute` options. |

Large datasets can be broken up and retrieved in chunks.  This allows you to work with a subset of results at once to keep your memory footprint under control.

`chunk` can be called on any query like you would call `get`.  You can stop the retrieving and processing early by returning `false` from the callback.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" ).chunk( 100, function( users ) {
    // Process the users here
    // Returning false from the callback stops processing
} );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## paginate

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| page | numeric | `false` | `1` | The page number to retrieve. |
| maxRows | numeric | `false` | `25` | The number of records per page.  If a number less than 0 is passed, 0 is used instead. |

Generates a pagination struct along with the results of the executed query.  It does this by calling both `count` and `forPage`.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .paginate();
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="Results" %}
```javascript
{
    "pagination": {
        "maxRows": 25,
        "offset": 0,
        "page": 1,
        "totalPages": 2,
        "totalRecords": 45
    },
    "results": [ { /* ... */ }, ]
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Custom Pagination Collectors

A pagination collector is the name given to the struct returned from calling the [`paginate`](retrieving-results.md#paginate) method.  It can be a struct or a component.  It needs one function defined and will be passed the following parameters.

#### generateWithResults

| Name | Type | Description |
| :--- | :--- | :--- |
| totalRecords | numeric | The total records count. |
| results | any | The results of the query execution.  It will be passed as whatever return format the user has defined. |
| page | numeric | The current page number. |
| maxRows | numeric | The maximum number of rows retrieved per page. |

You can set your custom pagination collector either in the constructor using the `paginationCollector` argument or by calling `setPaginationCollector` on a query builder instance.

By default, qb ships with [`cbpaginator`](https://forgebox.io/view/cbpaginator) as its pagination collector.  The return format of `cbpaginator` is the example shown above.

