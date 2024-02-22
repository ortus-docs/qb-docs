# Retrieving Results

## get

| Name    | Type            | Required | Default | Description                                                                                                    |
| ------- | --------------- | -------- | ------- | -------------------------------------------------------------------------------------------------------------- |
| columns | string \| array | false    |         | A shortcut parameter to retrieve only these columns overriding any columns previously set on the QueryBuilder. |
| options | struct          | false    | `{}`    | Any additional `queryExecute` options.                                                                         |

The `get` method is the most common method used for retrieving results. It executes using the configured `QueryBuilder` and returns the results.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" ).get();
```
{% endcode %}

{% code title="SQL (MySQL)" %}
```sql
SELECT * FROM `users`
```
{% endcode %}

`get` can also take a list or array of columns to use as a shortcut. If any are passed, those columns will be used instead of any columns previously set on the `QueryBuilder`.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" ).get( [ "id", "name" ] );
```
{% endcode %}

{% code title="SQL (MySQL)" %}
```sql
SELECT `id`, `name` FROM `users`
```
{% endcode %}

## first

| Name    | Type   | Required | Default | Description                            |
| ------- | ------ | -------- | ------- | -------------------------------------- |
| options | struct | `false`  | `{}`    | Any additional `queryExecute` options. |

If you just need to retrieve a single row from the database table, you may use the `first` method. This method will return a single record (a `Struct` by default). If no row is found an empty `Struct` will be returned by default.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" ).first();
```
{% endcode %}

{% code title="SQL (MySQL)" %}
```sql
SELECT * FROM `users`
 LIMIT(1)
```
{% endcode %}

## firstOrFail

| Name         | Type   | Required | Default | Description                                                                                                                                                         |
| ------------ | ------ | -------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| errorMessage | string | `false`  |         | An optional string error message or callback to produce a string error message. If a callback is used, it is passed the QueryBuilder instance as the only argument. |
| options      | struct | `false`  | `{}`    | Any additional `queryExecute` options.                                                                                                                              |

{% hint style="danger" %}
**throws:** `RecordNotFound`
{% endhint %}

Returns the first matching row for the configured query, just like [`first`](retrieving-results.md#first). If no records are found, it throws an `RecordNotFound` exception.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" ).firstOrFail();
```
{% endcode %}

{% code title="SQL (MySQL)" %}
```sql
SELECT * FROM `users`
 LIMIT(1)
```
{% endcode %}

## find

| Name     | Type     | Required | Default | Description                             |
| -------- | -------- | -------- | ------- | --------------------------------------- |
| id       | `any`    | true     |         | The id value to look up.                |
| idColumn | `string` | false    | `"id"`  | The name of the id column to constrain. |
| options  | `struct` | false    | `{}`    | Any additional `queryExecute` options.  |

Adds an id constraint to the query and returns the first record from the query.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" ).find( 1 );
```
{% endcode %}

{% code title="SQL (MySQL)" %}
```sql
SELECT * FROM `users`
WHERE `id` = ?
LIMIT(1)
```
{% endcode %}

## findOrFail

| Name     | Type     | Required | Default | Description                             |
| -------- | -------- | -------- | ------- | --------------------------------------- |
| id       | `any`    | true     |         | The id value to look up.                |
| idColumn | `string` | false    | `"id"`  | The name of the id column to constrain. |
| options  | `struct` | false    | `{}`    | Any additional `queryExecute` options.  |

{% hint style="danger" %}
**Throws:** `RecordNotFound`
{% endhint %}

Adds an id constraint to the query and returns the first record from the query. If no record is found, it throws an `RecordNotFound` exception.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" ).find( 415015 );
```
{% endcode %}

{% code title="SQL (MySQL)" %}
```sql
SELECT * FROM `users`
WHERE `id` = ?
LIMIT(1)
```
{% endcode %}

## values

| Name    | Type   | Required | Default | Description                                                                                                |
| ------- | ------ | -------- | ------- | ---------------------------------------------------------------------------------------------------------- |
| column  | any    | `true`   |         | The name of the column to retrieve or an [Expression](../building-queries/raw-expressions.md) to retrieve. |
| options | struct | `false`  | `{}`    | Any additional `queryExecute` options.                                                                     |

If you don't even need an entire row, you may extract a single value from each record using the `values` method. The `values` method will return the column of your choosing as a simple array.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" ).values( "firstName" );
```
{% endcode %}

{% code title="Result" %}
```
[ "jon", "jane", "jill", ... ]
```
{% endcode %}

An expression can also be passed to `values`:

```javascript
qb.from( "users" ).values( qb.raw( "CONCAT(fname, ' ', lname) AS fullName" ) );
```

{% hint style="info" %}
The [`valuesRaw`](retrieving-results.md#valuesraw) function can make this pattern more ergonomic.
{% endhint %}

## valuesRaw

| Name    | Type   | Required | Default | Description                                                                |
| ------- | ------ | -------- | ------- | -------------------------------------------------------------------------- |
| column  | string | `true`   |         | The sql to use as an [Expression](../building-queries/raw-expressions.md). |
| options | struct | `false`  | `{}`    | Any additional `queryExecute` options.                                     |

The `values` method will return the expression given for each row as a simple array.

```javascript
query.from( "users" ).valuesRaw( "CONCAT(fname, ' ', lname) AS fullName" );
```

## value

| Name              | Type    | Required | Default        | Description                                                                                                |
| ----------------- | ------- | -------- | -------------- | ---------------------------------------------------------------------------------------------------------- |
| column            | any     | `true`   |                | The name of the column to retrieve or an [Expression](../building-queries/raw-expressions.md) to retrieve. |
| defaultValue      | string  | `false`  | (empty string) | The default value returned if there are no records returned for the query.                                 |
| throwWhenNotFound | boolean | `false`  | `false`        | If `true`, it throws a `RecordCountException` if no records are returned from the query.                   |
| options           | struct  | `false`  | `{}`           | Any additional `queryExecute` options.                                                                     |

This method is similar to `values` except it only returns a single, simple value. Where `values` calls `get` under the hood, this method calls `first`.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" ).value( "firstName" );
```
{% endcode %}

{% code title="Result" %}
```
"jon"
```
{% endcode %}

If no records are returned from the query, one of two things will happen. If the `throwWhenNotFound` boolean is set to `true`, a `RecordCountException` will be thrown. Otherwise the `defaultValue` provided to the method will be returned.

An expression can also be passed to `value`:

```javascript
qb.from( "users" ).value( qb.raw( "CONCAT(fname, ' ', lname) AS fullName" ) );
```

{% hint style="info" %}
The [`valueRaw`](retrieving-results.md#valueraw) function can make this pattern more ergonomic.
{% endhint %}

## valueRaw

| Name              | Type    | Required | Default        | Description                                                                              |
| ----------------- | ------- | -------- | -------------- | ---------------------------------------------------------------------------------------- |
| column            | string  | `true`   |                | The sql to use as an [Expression](../building-queries/raw-expressions.md).               |
| defaultValue      | string  | `false`  | (empty string) | The default value returned if there are no records returned for the query.               |
| throwWhenNotFound | boolean | `false`  | `false`        | If `true`, it throws a `RecordCountException` if no records are returned from the query. |
| options           | struct  | `false`  | `{}`           | Any additional `queryExecute` options.                                                   |

The `value` method will return the expression given for the first row found.

```javascript
query.from( "users" ).valueRaw( "CONCAT(fname, ' ', lname) AS fullName" );
```

## chunk

| Name     | Type     | Default | Description                                       |
| -------- | -------- | ------- | ------------------------------------------------- |
| max      | numeric  |         | The number of results to return in each chunk.    |
| callback | Function |         | The function that will be called with each chunk. |
| options  | struct   | `{}`    | Any additional `queryExecute` options.            |

Large datasets can be broken up and retrieved in chunks. This allows you to work with a subset of results at once to keep your memory footprint under control.

`chunk` can be called on any query like you would call `get`. You can stop the retrieving and processing early by returning `false` from the callback.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" ).chunk( 100, function( users ) {
    // Process the users here
    // Returning false from the callback stops processing
} );
```
{% endcode %}

## paginate

| Name    | Type    | Required | Default | Description                                                                            |
| ------- | ------- | -------- | ------- | -------------------------------------------------------------------------------------- |
| page    | numeric | `false`  | `1`     | The page number to retrieve.                                                           |
| maxRows | numeric | `false`  | `25`    | The number of records per page.  If a number less than 0 is passed, 0 is used instead. |
| options | struct  | `false`  | `{}`    | Any additional `queryExecute` options.                                                 |

Generates a pagination struct along with the results of the executed query. It does this by calling both `count` and `forPage`.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .paginate();
```
{% endcode %}

{% code title="Results" %}
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
{% endcode %}

The behavior when a `maxRows` of 0 or lower is passed is determined by the `shouldMaxRowsOverrideToAll` callback function. The default callback returns all rows for values `<= 0`. You can customize this behavior by passing a new callback to the `shouldMaxRowsOverrideToAll` setting or `init` argument.

## simplePaginate

| Name    | Type    | Required | Default | Description                                                                            |
| ------- | ------- | -------- | ------- | -------------------------------------------------------------------------------------- |
| page    | numeric | `false`  | `1`     | The page number to retrieve.                                                           |
| maxRows | numeric | `false`  | `25`    | The number of records per page.  If a number less than 0 is passed, 0 is used instead. |
| options | struct  | `false`  | `{}`    | Any additional `queryExecute` options.                                                 |

Generates a simple pagination struct along with the results of the executed query. It does so without getting a `count` of the number of records the query would return.  This can be desirable for performance reasons if your query count is rather large.  It instead determines if there are more records by asking for one more row that your specified `maxRows`.  If the number of rows returned exceeds your specified `maxRows` then the pagination returns `hasMore: true`.  The `results` will always contain your specified `maxRows` (or less, if there aren't enough records).

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .simplePaginate();
```
{% endcode %}

{% code title="Results" %}
```javascript
{
    "pagination": {
        "maxRows": 25,
        "offset": 0,
        "page": 1,
        "hasMore": true
    },
    "results": [ { /* ... */ }, ]
}
```
{% endcode %}

The behavior when a `maxRows` of 0 or lower is passed is determined by the `shouldMaxRowsOverrideToAll` callback function. The default callback returns all rows for values `<= 0`. You can customize this behavior by passing a new callback to the `shouldMaxRowsOverrideToAll` setting or `init` argument.

### Custom Pagination Collectors

A pagination collector is the name given to the struct returned from calling the [`paginate`](retrieving-results.md#paginate) method. It can be a struct or a component. It needs one function defined and will be passed the following parameters.

#### generateWithResults

| Name         | Type    | Description                                                                                            |
| ------------ | ------- | ------------------------------------------------------------------------------------------------------ |
| totalRecords | numeric | The total records count.                                                                               |
| results      | any     | The results of the query execution.  It will be passed as whatever return format the user has defined. |
| page         | numeric | The current page number.                                                                               |
| maxRows      | numeric | The maximum number of rows retrieved per page.                                                         |

You can set your custom pagination collector either in the constructor using the `paginationCollector` argument or by calling `setPaginationCollector` on a query builder instance.

By default, qb ships with [`cbpaginator`](https://forgebox.io/view/cbpaginator) as its pagination collector. The return format of `cbpaginator` is the example shown above.

In qb 8.4.0 the `simplePaginate` method was added.  This uses a new method on the `paginationCollector`.

#### generateSimpleWithResults

| Name    | Type    | Description                                                                                            |
| ------- | ------- | ------------------------------------------------------------------------------------------------------ |
| results | any     | The results of the query execution.  It will be passed as whatever return format the user has defined. |
| page    | numeric | The current page number.                                                                               |
| maxRows | numeric | The maximum number of rows retrieved per page.                                                         |

{% hint style="info" %}
If you use a custom `paginationCollector`, ensure it has been updated with this new `generateSimpleWithResults` method before calling `simplePaginate`.
{% endhint %}
