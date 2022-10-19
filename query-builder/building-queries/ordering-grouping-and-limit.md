# Order By

The `orderBy` method seems simple but has a lot of depth depending on the type of arguments you pass in.

{% hint style="info" %}
Calling `orderBy` multiple times appends to the order list.
{% endhint %}

## Order By (String)

| Name      | Type   | Required | Default | Description                                                                                      |
| --------- | ------ | -------- | ------- | ------------------------------------------------------------------------------------------------ |
| column    | any    | `true`   |         | The name of the column to order by. An [`Expression`](raw-expressions.md) can be passed as well. |
| direction | string | `false`  | `"asc"` | The direction by which to order the query. Accepts `"asc"`or `"desc"`.                           |

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .orderBy( "email" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
ORDER BY `email` ASC
```
{% endcode %}

Calling `orderBy` multiple times will append to the order list.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .orderBy( "email" )
    .orderBy( "username", "desc" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
ORDER BY
  `email` ASC,
  `username` DESC
```
{% endcode %}

You can also provide an [`Expression`](raw-expressions.md).

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .orderBy( query.raw( "DATE(created_at)" ) );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
ORDER BY DATE(created_at)
```
{% endcode %}

## Order By (List)

| Name      | Type   | Required | Default | Description                                                                                                                                                                                                     |           |
| --------- | ------ | -------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------- |
| column    | any    | `true`   |         | The list of the columns to order by.  Each column can optionally declare it's sort direction after a pipe delimiter. (e.g. \`"height                                                                            | desc"\`). |
| direction | string | `false`  | `"asc"` | The direction by which to order the query. Accepts `"asc"`or `"desc"`.  This value will be used as the default value for all entries in the column list that fail to specify a direction for a specific column. |           |

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .orderBy( "email|asc,username", "desc" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
ORDER BY
  `email` ASC,
  `username` DESC
```
{% endcode %}

## Order By (Array of Strings)

| Name      | Type   | Required | Default | Description                                                                                                                                                                                                      |           |
| --------- | ------ | -------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------- |
| column    | any    | `true`   |         | The array of the columns to order by.  Each column can optionally declare it's sort direction after a pipe delimiter. (e.g. \`"height                                                                            | desc"\`). |
| direction | string | `false`  | `"asc"` | The direction by which to order the query. Accepts `"asc"`or `"desc"`.  This value will be used as the default value for all entries in the column array that fail to specify a direction for a specific column. |           |

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .orderBy( [ "email|asc", "username" ], "desc" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
ORDER BY
  `email` ASC,
  `username` DESC
```
{% endcode %}

## Order By (Array of Structs)

| Name      | Type   | Required | Default | Description                                                                                                                                                                                                                                   |
| --------- | ------ | -------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| column    | any    | `true`   |         | The array of the columns to order by.  Each column can optionally declare it's sort direction using a struct.  The struct should have a column key and an optional direction key. (e.g. `{ column = "favorite_color", direction = "desc" }`). |
| direction | string | `false`  | `"asc"` | The direction by which to order the query. Accepts `"asc"`or `"desc"`.  This value will be used as the default value for all entries in the column array that fail to specify a direction for a specific column.                              |

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .orderBy( [
        { "column": "email", "direction": "asc" },
        "username"
    ], "desc" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
ORDER BY
  `email` ASC,
  `username` DESC
```
{% endcode %}

## Order By (Subquery)

| Name      | Type   | Required | Default | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| --------- | ------ | -------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| column    | any    | `true`   |         | The name of the column to order by. An [`Expression`](raw-expressions.md) can be passed as well. An array can be passed with any combination of simple values, array, struct, or list for each entry in the array (an example with all possible value styles: column = \[ "last\_name", \[ "age", "desc" ], { column = "favorite\_color", direction = "desc" }, "height\|desc" ];. The column argument can also just accept a comman delimited list with a pipe ( \| ) as the secondary delimiter denoting the direction of the order by. The pipe delimiter is also used when parsing the column argument when it is passed as an array and the entry in the array is a pipe delimited string. |
| direction | string | `false`  | `"asc"` | Ignored when using a Function or QueryBuilder instance.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |

You can order with a subquery using either a function or a QueryBuilder instance.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .orderBy( function( q ) {
        q.selectRaw( "MAX(created_date)" )
            .from( "logins" )
            .whereColumn( "users.id", "logins.user_id" );
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
ORDER BY (
    SELECT MAX(created_date)
    FROM `logins`
    WHERE `users`.`id` = `logins`.`user_id`
)
```
{% endcode %}



## Order By Raw

| Name       | Type   | Required | Default | Description                                |
| ---------- | ------ | -------- | ------- | ------------------------------------------ |
| expression | string | `true`   |         | The raw SQL expression to use.             |
| bindings   | array  | `false`  | `[]`    | Any bindings (`?`) used in the expression. |

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .orderByRaw( "CASE WHEN status = ? THEN 1 ELSE 0 END DESC", [ 1 ] );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
ORDER BY CASE WHEN status = ? THEN 1 ELSE 0 END DESC
```
{% endcode %}

## clearOrders

| Name         | Type | Required | Default | Description |
| ------------ | ---- | -------- | ------- | ----------- |
| No arguments |      | ``       |         |             |

Clears the currently configured orders for the query.  Usually used by downstream libraries like [Quick](https://quick.ortusbooks.com/).

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .orderBy( "email" )
    .clearOrders();
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
```
{% endcode %}

## reorder

| Name      | Type   | Required | Default | Description                                                                                      |
| --------- | ------ | -------- | ------- | ------------------------------------------------------------------------------------------------ |
| column    | any    | `true`   |         | The name of the column to order by. An [`Expression`](raw-expressions.md) can be passed as well. |
| direction | string | `false`  | `"asc"` | The direction by which to order the query. Accepts `"asc"`or `"desc"`.                           |

Clears the currently configured orders for the query and sets the new orders passed in.  Any valid argument to [`orderBy`](ordering-grouping-and-limit.md) can be passed here.  Usually used by downstream libraries like [Quick](https://quick.ortusbooks.com/).

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .orderBy( "email" )
    .reorder( "username" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
ORDER BY `username` ASC
```
{% endcode %}
