# Wheres

| Table of Contents                      |                                              |                                      |
| -------------------------------------- | -------------------------------------------- | ------------------------------------ |
| [where](wheres.md#where)               | [andWhere](wheres.md#andwhere)               | [orWhere](wheres.md#orwhere)         |
| [whereBetween](wheres.md#wherebetween) | [whereNotBetween](wheres.md#wherenotbetween) | [whereColumn](wheres.md#wherecolumn) |
| [whereExists](wheres.md#whereexists)   | [whereNotExists](wheres.md#wherenotexists)   | [whereLike](wheres.md#wherelike)     |
| [whereIn](wheres.md#wherein)           | [whereNotIn](wheres.md#wherenotin)           | [whereRaw](wheres.md#whereraw)       |
| [whereNull](wheres.md#wherenull)       | [whereNotNull](wheres.md#wherenotnull)       |                                      |

## Where Methods

### where

| Name       | Type                                                   | Required | Default | Description                                                                                                                                                                                                                         |
| ---------- | ------------------------------------------------------ | -------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| column     | string \| [Expression](raw-expressions.md) \| Function | `true`   |         | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query. A function can be passed to begin a nested where statement.                                                                         |
| operator   | string \| [Expression](raw-expressions.md)             | `false`  |         | The operator to use for the constraint (i.e. "=", "<", ">=", etc.). A value can be passed as the `operator` and the `value` left null as a shortcut for equals (e.g. where( "column", 1 ) == where( "column", "=", 1 ) ).           |
| value      | any                                                    | `false`  |         | The value with which to constrain the column.  An [`Expression`](raw-expressions.md) can be passed as well.  If a QueryBuilder or Function is passed, it will be used as a subselect expression.                                    |
| combinator | string                                                 | `false`  | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the [`andWhere`](wheres.md#andwhere) and [`orWhere`](wheres.md#orwhere) methods instead. |

Adds a where clause to a query.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "active", "=", 1 );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `active` = ?
```
{% endcode %}

{% hint style="info" %}
Using the `where` method will parameterize the `value` passed.  If you want to constrain a column to another column, use the [`whereColumn`](wheres.md#wherecolumn) method.
{% endhint %}

You can also pass an [Expression](raw-expressions.md) as the value.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "last_logged_in", ">", query.raw( "NOW()" ) );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `last_logged_in` > NOW()
```
{% endcode %}

Any of the following operators can be used in a where clause.

| Valid Operators |                |             |
| --------------- | -------------- | ----------- |
| =               | <              | >           |
| <=              | >=             | <>          |
| !=              | like           | like binary |
| not like        | between        | ilike       |
| &               | \|             | ^           |
| <<              | >>             | rlike       |
| regexp          | not regexp     | \~          |
| \~\*            | !\~            | !\~\*       |
| similar to      | not similar to |             |

When using the `"="` constraint, you can use a shortcut and define the value as the second argument.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "active", 1 );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `active` = ?
```
{% endcode %}

{% hint style="info" %}
You may also use [dynamic where{Column}](wheres.md#dynamic-where-methods) statements to simplify this further.
{% endhint %}

To group where statements together, pass a function to the where clause as the only parameter.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( function( q ) {
        q.where( "active", 1 )
            .where( "last_logged_in", ">", dateAdd( "ww", -1, now() ) )
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE (
    `active` = ?
    AND
    `last_logged_in` > ?
)
```
{% endcode %}

{% hint style="info" %}
This grouping can be nested as many levels as you require.
{% endhint %}

A Function or QueryBuilder can be used as a subselect expression when passed to `value`.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "email", "foo" )
    .orWhere( "id", "=", function( q ) {
        q.select( q.raw( "MAX(id)" ) )
            .from( "users" )
            .where( "email", "bar" );
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `email` = ?
  OR `id` = (
    SELECT MAX(id)
    FROM `users`
    WHERE `email` = ?
  )
```
{% endcode %}

### andWhere

| Name     | Type                                                   | Required | Default | Description                                                                                                                                                                                                               |
| -------- | ------------------------------------------------------ | -------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| column   | string \| [Expression](raw-expressions.md) \| Function | `true`   |         | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query. A function can be passed to begin a nested where statement.                                                               |
| operator | string \| [Expression](raw-expressions.md)             | `false`  |         | The operator to use for the constraint (i.e. "=", "<", ">=", etc.). A value can be passed as the `operator` and the `value` left null as a shortcut for equals (e.g. where( "column", 1 ) == where( "column", "=", 1 ) ). |
| value    | any                                                    | `false`  |         | The value with which to constrain the column.  An [`Expression`](raw-expressions.md) can be passed as well.  If a QueryBuilder or Function is passed, it will be used as a subselect expression.                          |

This method is simply an alias for [`where`](wheres.md#where) with the combinator set to `"and"`.

### orWhere

| Name     | Type                                                   | Required | Default | Description                                                                                                                                                                                                               |
| -------- | ------------------------------------------------------ | -------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| column   | string \| [Expression](raw-expressions.md) \| Function | `true`   |         | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query. A function can be passed to begin a nested where statement.                                                               |
| operator | string \| [Expression](raw-expressions.md)             | `false`  |         | The operator to use for the constraint (i.e. "=", "<", ">=", etc.). A value can be passed as the `operator` and the `value` left null as a shortcut for equals (e.g. where( "column", 1 ) == where( "column", "=", 1 ) ). |
| value    | any                                                    | `false`  |         | The value with which to constrain the column.  An [`Expression`](raw-expressions.md) can be passed as well.  If a QueryBuilder or Function is passed, it will be used as a subselect expression.                          |

This method is simply an alias for [`where`](wheres.md#where) with the combinator set to `"or"`.

### whereBetween

| Name       | Type                            | Required | Default | Description                                                                                                                                                                                                                        |
| ---------- | ------------------------------- | -------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| column     | string \| Expression            | `true`   |         | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query.                                                                                                                                    |
| start      | any \| Function \| QueryBuilder | `true`   |         | The beginning value of the BETWEEN statement.  If a function or QueryBuilder is passed it is used as a subselect expression.                                                                                                       |
| end        | any \| Function \| QueryBuilder | `true`   |         | The end value of the BETWEEN statement. If a function or QueryBuilder is passed it is used as a subselect expression.                                                                                                              |
| combinator | string                          | `false`  | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods](wheres.md#andwhere-and-orwhere) instead. |
| negate     | boolean                         | `false`  | `false` | False for BETWEEN, True for NOT BETWEEN.                                                                                                                                                                                           |

Adds a where between clause to the query.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereBetween( "id", 1, 2 );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `id` BETWEEN ? AND ?
```
{% endcode %}

If a function or QueryBuilder is passed it is used as a subselect expression.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereBetween(
        "id",
        function( q ) {
            q.select( q.raw( "MIN(id)" ) )
                .from( "users" )
                .where( "email", "bar" );
        },
        builder.newQuery()
            .select( builder.raw( "MAX(id)" ) )
            .from( "users" )
            .where( "email", "bar" )
    );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `id` BETWEEN (
    SELECT MIN(id)
    FROM `users`
    WHERE `email` = ?
)
AND (
    SELECT MAX(id)
    FROM `users`
    WHERE `email` = ?
)
```
{% endcode %}

### whereNotBetween

| Name       | Type                            | Required | Default | Description                                                                                                                                                                                                                        |
| ---------- | ------------------------------- | -------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| column     | string \| Expression            | `true`   |         | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query.                                                                                                                                    |
| start      | any \| Function \| QueryBuilder | `true`   |         | The beginning value of the BETWEEN statement.  If a function or QueryBuilder is passed it is used as a subselect expression.                                                                                                       |
| end        | any \| Function \| QueryBuilder | `true`   |         | The end value of the BETWEEN statement. If a function or QueryBuilder is passed it is used as a subselect expression.                                                                                                              |
| combinator | string                          | `false`  | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods](wheres.md#andwhere-and-orwhere) instead. |

Adds a where not in clause to the query.  This behaves identically to the [`whereBetween`](wheres.md#wherebetween) method with the `negate`flag set to `true`.  See the documentation for [`whereBetween`](wheres.md#wherebetween) for usage and examples.

### whereColumn

| Name       | Type                                       | Required | Default | Description                                                                                                                                                                                                                        |
| ---------- | ------------------------------------------ | -------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| first      | string \| [Expression](raw-expressions.md) | `true`   |         | The name of the first column or [`Expression`](raw-expressions.md) with which to constrain the query.                                                                                                                              |
| operator   | string \| [Expression](raw-expressions.md) | `true`   |         | The operator to use for the constraint (i.e. "=", "<", ">=", etc.). A value can be passed as the `operator` and the `value` left null as a shortcut for equals (e.g. where( "column", 1 ) == where( "column", "=", 1 ) ).          |
| second     | string \| Expression                       | `false`  |         | The name of the second column or [`Expression`](raw-expressions.md) with which to constrain the query.                                                                                                                             |
| combinator | string                                     | `false`  | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods](wheres.md#andwhere-and-orwhere) instead. |

Adds a where clause to a query that compares two columns.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereColumn( "first_name", "=", "last_name" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `first_name` = `last_name`
```
{% endcode %}

Just as with `where`, when using `"="` as the operator you can use a shorthand passing the second column in as the operator and leaving the second column `null`.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereColumn( "first_name", "last_name" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `first_name` = `last_name`
```
{% endcode %}

`Expressions` can be passed in place of either column.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereColumn( "first_name", query.raw( "LOWER(first_name)" ) );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `first_name` = LOWER(first_name)
```
{% endcode %}

### whereExists

| Name       | Type                     | Required | Default | Description                                                                                                                                                                                                                        |
| ---------- | ------------------------ | -------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| query      | Function \| QueryBuilder | `true`   |         | A function or QueryBuilder instance to be used as the exists subquery.                                                                                                                                                             |
| combinator | string                   | `false`  | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods ](wheres.md#andwhere-and-orwhere)instead. |
| negate     | boolean                  | `false`  | `false` | False for EXISTS, True for NOT EXISTS.                                                                                                                                                                                             |

Adds a where exists clause to the query.

It can be configured with a function.

{% code title="QueryBuilder" %}
```javascript
query.from( "orders" )
    .whereExists( function( q ) {
        q.select( q.raw( 1 ) )
            .from( "products" )
            .whereColumn( "products.id", "orders.id" );
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `orders`
WHERE EXISTS (
    SELECT 1
    FROM `products`
    WHERE `products`.`id` = `orders`.`id`
)
```
{% endcode %}

It can also be configured with a QueryBuilder instance.

{% code title="QueryBuilder" %}
```javascript
var existsQuery = query.newQuery()
    .select( q.raw( 1 ) )
    .from( "products" )
    .whereColumn( "products.id", "orders.id" );

query.from( "orders" )
    .whereExists( existsQuery );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `orders`
WHERE EXISTS (
    SELECT 1
    FROM `products`
    WHERE `products`.`id` = `orders`.`id`
)
```
{% endcode %}

### whereNotExists

| Name       | Type                     | Required | Default | Description                                                                                                                                                                                                                        |
| ---------- | ------------------------ | -------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| query      | Function \| QueryBuilder | `true`   |         | A function or QueryBuilder instance to be used as the not exists subquery.                                                                                                                                                         |
| combinator | string                   | `false`  | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods ](wheres.md#andwhere-and-orwhere)instead. |

Adds a where not in clause to the query.  This behaves identically to the [`whereExists`](wheres.md#whereexists) method with the `negate`flag set to `true`.  See the documentation for [`whereExists`](wheres.md#whereexists) for usage and examples.

### whereLike

| Name       | Type                                       | Required | Default | Description                                                                                                                                                                                                                        |
| ---------- | ------------------------------------------ | -------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| column     | string \| [Expression](raw-expressions.md) | `true`   |         | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query.                                                                                                                                    |
| value      | any                                        | `false`  |         | The value with which to constrain the column.  An [`Expression`](raw-expressions.md) can be passed as well.  If a QueryBuilder or Function is passed, it will be used as a subselect expression.                                   |
| combinator | string                                     | `false`  | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods ](wheres.md#andwhere-and-orwhere)instead. |

A shortcut for calling [`where`](wheres.md#where) with `"like"` set as the operator.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereLike( "username", "J%" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `username` LIKE ?
```
{% endcode %}

### whereNotLike

| Name       | Type                                       | Required | Default | Description                                                                                                                                                                                                                        |
| ---------- | ------------------------------------------ | -------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| column     | string \| [Expression](raw-expressions.md) | `true`   |         | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query.                                                                                                                                    |
| value      | any                                        | `false`  |         | The value with which to constrain the column.  An [`Expression`](raw-expressions.md) can be passed as well.  If a QueryBuilder or Function is passed, it will be used as a subselect expression.                                   |
| combinator | string                                     | `false`  | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods ](wheres.md#andwhere-and-orwhere)instead. |

A shortcut for calling [`where`](wheres.md#where) with `"not like"` set as the operator.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereNotLike( "username", "J%" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `username` NOT LIKE ?
```
{% endcode %}

### whereIn

| Name       | Type                                                                            | Required | Default | Description                                                                                                                                                                                                                                                             |
| ---------- | ------------------------------------------------------------------------------- | -------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| column     | string \| Expression                                                            | `true`   |         | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query.                                                                                                                                                                         |
| values     | string \| array \| [Expression](raw-expressions.md) \| Function \| QueryBuilder | `true`   |         | A single value, list of values, or array of values to constrain a column with.  [`Expressions`](raw-expressions.md) may be used in any place a value is used.  Alternatively, a function or QueryBuilder instance can be passed in to be used as a subquery expression. |
| combinator | string                                                                          | `false`  | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods ](wheres.md#andwhere-and-orwhere)instead.                                      |
| negate     | boolean                                                                         | `false`  | `false` | False for IN, True for NOT IN.                                                                                                                                                                                                                                          |

Adds a where in clause to the query.

The values passed to `whereIn` can be a single value, a list of values, or an array of values.

{% code title="QueryBuilder" %}
```javascript
query.from( "orders" )
    .whereIn( "id", [ 1, 4, 66 ] );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `orders`
WHERE `id` IN (?, ?, ?)
```
{% endcode %}

{% hint style="warning" %}
Some database grammars have a hard limit on the number of parameters passed to a SQL statement.  Keep this in mind while writing your queries.
{% endhint %}

If a list of values is passed in, it is converted to an array of values using a single comma (`","`) delimiter.

{% code title="QueryBuilder" %}
```javascript
query.from( "orders" )
    .whereIn( "id", "1,4,66" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `orders`
WHERE `id` IN (?, ?, ?)
```
{% endcode %}

Any value in the list or array can also be passed using a [custom parameter type](parameters-and-bindings.md#custom-parameter-types) to have more control over the parameter settings.

{% code title="QueryBuilder" %}
```javascript
query.from( "orders" )
    .whereIn( "id", [ 1, 4, { value = "66", cfsqltype = "CF_SQL_VARCHAR" } ] );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `orders`
WHERE `id` IN (?, ?, ?)
```
{% endcode %}

`Expressions` can be freely mixed in with other values.

{% code title="QueryBuilder" %}
```javascript
query.from( "orders" )
    .whereIn( "id", [ query.raw( "MAX(id)" ), 4, 66 ] );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `orders`
WHERE `id` IN (MAX(id), ?, ?)
```
{% endcode %}

A function or QueryBuilder instance can be passed to be used as a subquery expression instead of a list of values.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereIn( "id", function( q ) {
        q.select( "id" )
            .from( "users" )
            .where( "age", ">", 25 );
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE IN (
    SELECT `id`
    FROM `users`
    WHERE `age` > ?
)
```
{% endcode %}

{% hint style="warning" %}
You may find a `whereExists` method performs better for you than a `whereIn` with a subquery.
{% endhint %}

### whereNotIn

| Name       | Type                                                                            | Required | Default | Description                                                                                                                                                                                                                                                             |
| ---------- | ------------------------------------------------------------------------------- | -------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| column     | string \| Expression                                                            | `true`   |         | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query.                                                                                                                                                                         |
| values     | string \| array \| [Expression](raw-expressions.md) \| Function \| QueryBuilder | `true`   |         | A single value, list of values, or array of values to constrain a column with.  [`Expressions`](raw-expressions.md) may be used in any place a value is used.  Alternatively, a function or QueryBuilder instance can be passed in to be used as a subquery expression. |
| combinator | string                                                                          | `false`  | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods ](wheres.md#andwhere-and-orwhere)instead.                                      |

Adds a where not in clause to the query.  This behaves identically to the `whereIn` method with the `negate`flag set to `true`.  See the documentation for [`whereIn`](wheres.md#wherein) for usage and examples.

### whereRaw

| Name          | Type   | Required | Default | Description                                                                                                                                                                                                                        |
| ------------- | ------ | -------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| sql           | string | `true`   |         | The raw SQL to add to the query.                                                                                                                                                                                                   |
| whereBindings | array  | `false`  | `[]`    | Any bindings needed for the raw SQL.  Bindings can be simple values or [custom parameters](parameters-and-bindings.md#custom-parameter-types).                                                                                     |
| combinator    | string | `false`  | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods ](wheres.md#andwhere-and-orwhere)instead. |

Shorthand to add a raw SQL statement to the where clauses.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereRaw(
        "id = ? OR email = ? OR is_admin = 1",
        [ 1, "foo" ]
    );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE id = ? OR email = ? OR is_admin = 1
```
{% endcode %}

### whereNull

| Name       | Type                 | Required | Default | Description                                                                                                                                                                                                                        |
| ---------- | -------------------- | -------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| column     | string \| Expression | `true`   |         | The name of the column to check if it is NULL.  Can also pass an [`Expression`](raw-expressions.md).                                                                                                                               |
| combinator | string               | `false`  | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods ](wheres.md#andwhere-and-orwhere)instead. |
| negate     | boolean              | `false`  | `false` | False for NULL, True for NOT NULL.                                                                                                                                                                                                 |

Adds a where null clause to the query.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereNull( "id" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `id` IS NULL
```
{% endcode %}

### whereNotNull

| Name       | Type                 | Required | Default | Description                                                                                                                                                                                                                        |
| ---------- | -------------------- | -------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| column     | string \| Expression | `true`   |         | The name of the column to check if it is NULL.  Can also pass an [`Expression`](raw-expressions.md).                                                                                                                               |
| combinator | string               | `false`  | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods ](wheres.md#andwhere-and-orwhere)instead. |
| negate     | boolean              | `false`  | `false` | False for NULL, True for NOT NULL.                                                                                                                                                                                                 |

Adds a where not in clause to the query.  This behaves identically to the [`whereNull`](wheres.md#wherenull) method with the `negate`flag set to `true`.  See the documentation for [`whereNull`](wheres.md#wherenull) for usage and examples.

## Dynamic Where Methods

qb uses `onMissingMethod` to provide a few different helpers when working with `where...` methods.

### andWhere... and orWhere...

Every `where...` method in qb can be called prefixed with either `and` or `or`.  Doing so will call the original method using the corresponding combinator.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "username", "like", "j%" )
    .andWhere( function( q ) {
        q.where( "isSubscribed", 1 )
            .orWhere( "isOnFreeTrial", 1 );
     } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `username` LIKE ?
  AND (
    `isSubscribed` = ?
    OR
    `isOnFreeTrial` = ?
  )
```
{% endcode %}

### where{Column}

If you call a method starting with `where` that does not match an existing qb method, qb will instead call the `where` method using the rest of the method name as the first column name.  (The rest of the arguments will be shifted to account for this.)  This also applies to `andWhere{Column}` and `orWhere{Column}` method signatures.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereUsername( "like", "j%" )
    .whereActive( 1 );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `username` LIKE ?
  AND `active` = ?
```
{% endcode %}
