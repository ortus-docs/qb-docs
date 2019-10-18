# Wheres

| Table of Contents |  |  |
| :--- | :--- | :--- |
| [where](wheres.md#where) | [andWhere](wheres.md#andwhere) | [orWhere](wheres.md#orwhere) |
| [whereBetween](wheres.md#wherebetween) | [whereNotBetween](wheres.md#wherenotbetween) | [whereColumn](wheres.md#wherecolumn) |
| [whereExists](wheres.md#whereexists) | [whereNotExists](wheres.md#wherenotexists) | [whereLike](wheres.md#wherelike) |
| [whereIn](wheres.md#wherein) | [whereNotIn](wheres.md#wherenotin) | [whereRaw](wheres.md#whereraw) |
| [whereNull](wheres.md#wherenull) | [whereNotNull](wheres.md#wherenotnull) |  |

## Where Methods

### where

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| column | string \| [Expression](raw-expressions.md) \| Function | `true` |  | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query. A function can be passed to begin a nested where statement. |
| operator | string \| [Expression](raw-expressions.md) | `false` |  | The operator to use for the constraint \(i.e. "=", "&lt;", "&gt;=", etc.\). A value can be passed as the `operator` and the `value` left null as a shortcut for equals \(e.g. where\( "column", 1 \) == where\( "column", "=", 1 \) \). |
| value | any | `false` |  | The value with which to constrain the column.  An [`Expression`](raw-expressions.md) can be passed as well.  If a QueryBuilder or Function is passed, it will be used as a subselect expression. |
| combinator | string | `false` | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the [`andWhere`](wheres.md#andwhere) and [`orWhere`](wheres.md#orwhere) methods instead. |

Adds a where clause to a query.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "active", "=", 1 );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `active` = ?
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="info" %}
Using the `where` method will parameterize the `value` passed.  If you want to constrain a column to another column, use the [`whereColumn`](wheres.md#wherecolumn) method.
{% endhint %}

You can also pass an [Expression](raw-expressions.md) as the value.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "last_logged_in", ">", query.raw( "NOW()" ) );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `last_logged_in` > NOW()
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Any of the following operators can be used in a where clause.

| Valid Operators |  |  |
| :--- | :--- | :--- |
| = | &lt; | &gt; |
| &lt;= | &gt;= | &lt;&gt; |
| != | like | like binary |
| not like | between | ilike |
| & | \| | ^ |
| &lt;&lt; | &gt;&gt; | rlike |
| regexp | not regexp | ~ |
| ~\* | !~ | !~\* |
| similar to | not similar to |  |

When using the `"="` constraint, you can use a shortcut and define the value as the second argument.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "active", 1 );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `active` = ?
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="info" %}
You may also use [dynamic where{Column}](wheres.md#dynamic-where-methods) statements to simplify this further.
{% endhint %}

To group where statements together, pass a function to the where clause as the only parameter.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( function( q ) {
        q.where( "active", 1 )
            .where( "last_logged_in", ">", dateAdd( "ww", -1, now() ) )
    } );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE (
    `active` = ?
    AND
    `last_logged_in` > ?
)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="info" %}
This grouping can be nested as many levels as you require.
{% endhint %}

A Function or QueryBuilder can be used as a subselect expression when passed to `value`.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "email", "foo" )
    .orWhere( "id", "=", function( q ) {
        q.select( q.raw( "MAX(id)" ) )
            .from( "users" )
            .where( "email", "bar" );
    } );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
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
{% endcode-tabs-item %}
{% endcode-tabs %}

### andWhere

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| column | string \| [Expression](raw-expressions.md) \| Function | `true` |  | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query. A function can be passed to begin a nested where statement. |
| operator | string \| [Expression](raw-expressions.md) | `false` |  | The operator to use for the constraint \(i.e. "=", "&lt;", "&gt;=", etc.\). A value can be passed as the `operator` and the `value` left null as a shortcut for equals \(e.g. where\( "column", 1 \) == where\( "column", "=", 1 \) \). |
| value | any | `false` |  | The value with which to constrain the column.  An [`Expression`](raw-expressions.md) can be passed as well.  If a QueryBuilder or Function is passed, it will be used as a subselect expression. |

This method is simply an alias for [`where`](wheres.md#where) with the combinator set to `"and"`.

### orWhere

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| column | string \| [Expression](raw-expressions.md) \| Function | `true` |  | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query. A function can be passed to begin a nested where statement. |
| operator | string \| [Expression](raw-expressions.md) | `false` |  | The operator to use for the constraint \(i.e. "=", "&lt;", "&gt;=", etc.\). A value can be passed as the `operator` and the `value` left null as a shortcut for equals \(e.g. where\( "column", 1 \) == where\( "column", "=", 1 \) \). |
| value | any | `false` |  | The value with which to constrain the column.  An [`Expression`](raw-expressions.md) can be passed as well.  If a QueryBuilder or Function is passed, it will be used as a subselect expression. |

This method is simply an alias for [`where`](wheres.md#where) with the combinator set to `"or"`.

### whereBetween

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| column | string \| Expression | `true` |  | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query. |
| start | any \| Function \| QueryBuilder | `true` |  | The beginning value of the BETWEEN statement.  If a function or QueryBuilder is passed it is used as a subselect expression. |
| end | any \| Function \| QueryBuilder | `true` |  | The end value of the BETWEEN statement. If a function or QueryBuilder is passed it is used as a subselect expression. |
| combinator | string | `false` | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods](wheres.md#andwhere-and-orwhere) instead. |
| negate | boolean | `false` | `false` | False for BETWEEN, True for NOT BETWEEN. |

Adds a where between clause to the query.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereBetween( "id", 1, 2 );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `id` BETWEEN ? AND ?
```
{% endcode-tabs-item %}
{% endcode-tabs %}

If a function or QueryBuilder is passed it is used as a subselect expression.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
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
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
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
{% endcode-tabs-item %}
{% endcode-tabs %}

### whereNotBetween

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| column | string \| Expression | `true` |  | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query. |
| start | any \| Function \| QueryBuilder | `true` |  | The beginning value of the BETWEEN statement.  If a function or QueryBuilder is passed it is used as a subselect expression. |
| end | any \| Function \| QueryBuilder | `true` |  | The end value of the BETWEEN statement. If a function or QueryBuilder is passed it is used as a subselect expression. |
| combinator | string | `false` | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods](wheres.md#andwhere-and-orwhere) instead. |

Adds a where not in clause to the query.  This behaves identically to the [`whereBetween`](wheres.md#wherebetween) method with the `negate`flag set to `true`.  See the documentation for [`whereBetween`](wheres.md#wherebetween) for usage and examples.

### whereColumn

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| first | string \| [Expression](raw-expressions.md) | `true` |  | The name of the first column or [`Expression`](raw-expressions.md) with which to constrain the query. |
| operator | string \| [Expression](raw-expressions.md) | `true` |  | The operator to use for the constraint \(i.e. "=", "&lt;", "&gt;=", etc.\). A value can be passed as the `operator` and the `value` left null as a shortcut for equals \(e.g. where\( "column", 1 \) == where\( "column", "=", 1 \) \). |
| second | string \| Expression | `false` |  | The name of the second column or [`Expression`](raw-expressions.md) with which to constrain the query. |
| combinator | string | `false` | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods](wheres.md#andwhere-and-orwhere) instead. |

Adds a where clause to a query that compares two columns.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereColumn( "first_name", "=", "last_name" );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `first_name` = `last_name`
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Just as with `where`, when using `"="` as the operator you can use a shorthand passing the second column in as the operator and leaving the second column `null`.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereColumn( "first_name", "last_name" );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `first_name` = `last_name`
```
{% endcode-tabs-item %}
{% endcode-tabs %}

`Expressions` can be passed in place of either column.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereColumn( "first_name", query.raw( "LOWER(first_name)" ) );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `first_name` = LOWER(first_name)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### whereExists

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| query | Function \| QueryBuilder | `true` |  | A function or QueryBuilder instance to be used as the exists subquery. |
| combinator | string | `false` | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods ](wheres.md#andwhere-and-orwhere)instead. |
| negate | boolean | `false` | `false` | False for EXISTS, True for NOT EXISTS. |

Adds a where exists clause to the query.

It can be configured with a function.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "orders" )
    .whereExists( function( q ) {
        q.select( q.raw( 1 ) )
            .from( "products" )
            .whereColumn( "products.id", "orders.id" );
    } );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `orders`
WHERE EXISTS (
    SELECT 1
    FROM `products`
    WHERE `products`.`id` = `orders`.`id`
)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

It can also be configured with a QueryBuilder instance.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
var existsQuery = query.newQuery()
    .select( q.raw( 1 ) )
    .from( "products" )
    .whereColumn( "products.id", "orders.id" );

query.from( "orders" )
    .whereExists( existsQuery );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `orders`
WHERE EXISTS (
    SELECT 1
    FROM `products`
    WHERE `products`.`id` = `orders`.`id`
)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### whereNotExists

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| query | Function \| QueryBuilder | `true` |  | A function or QueryBuilder instance to be used as the not exists subquery. |
| combinator | string | `false` | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods ](wheres.md#andwhere-and-orwhere)instead. |

Adds a where not in clause to the query.  This behaves identically to the [`whereExists`](wheres.md#whereexists) method with the `negate`flag set to `true`.  See the documentation for [`whereExists`](wheres.md#whereexists) for usage and examples.

### whereLike

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| column | string \| [Expression](raw-expressions.md) | `true` |  | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query. |
| value | any | `false` |  | The value with which to constrain the column.  An [`Expression`](raw-expressions.md) can be passed as well.  If a QueryBuilder or Function is passed, it will be used as a subselect expression. |
| combinator | string | `false` | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods ](wheres.md#andwhere-and-orwhere)instead. |

A shortcut for calling [`where`](wheres.md#where) with `"like"` set as the operator.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereLike( "username", "J%" );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `username` LIKE ?
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### whereIn

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| column | string \| Expression | `true` |  | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query. |
| values | string \| array \| [Expression](raw-expressions.md) \| Function \| QueryBuilder | `true` |  | A single value, list of values, or array of values to constrain a column with.  [`Expressions`](raw-expressions.md) may be used in any place a value is used.  Alternatively, a function or QueryBuilder instance can be passed in to be used as a subquery expression. |
| combinator | string | `false` | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods ](wheres.md#andwhere-and-orwhere)instead. |
| negate | boolean | `false` | `false` | False for IN, True for NOT IN. |

Adds a where in clause to the query.

The values passed to `whereIn` can be a single value, a list of values, or an array of values.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "orders" )
    .whereIn( "id", [ 1, 4, 66 ] );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `orders`
WHERE `id` IN (?, ?, ?)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="warning" %}
Some database grammars have a hard limit on the number of parameters passed to a SQL statement.  Keep this in mind while writing your queries.
{% endhint %}

If a list of values is passed in, it is converted to an array of values using a single comma \(`","`\) delimiter.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "orders" )
    .whereIn( "id", "1,4,66" );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `orders`
WHERE `id` IN (?, ?, ?)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Any value in the list or array can also be passed using a [custom parameter type](parameters-and-bindings.md#custom-parameter-types) to have more control over the parameter settings.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "orders" )
    .whereIn( "id", [ 1, 4, { value = "66", cfsqltype = "CF_SQL_VARCHAR" } ] );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `orders`
WHERE `id` IN (?, ?, ?)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

`Expressions` can be freely mixed in with other values.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "orders" )
    .whereIn( "id", [ query.raw( "MAX(id)" ), 4, 66 ] );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `orders`
WHERE `id` IN (MAX(id), ?, ?)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

A function or QueryBuilder instance can be passed to be used as a subquery expression instead of a list of values.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereIn( "id", function( q ) {
        q.select( "id" )
            .from( "users" )
            .where( "age", ">", 25 );
    } );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE IN (
    SELECT `id`
    FROM `users`
    WHERE `age` > ?
)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="warning" %}
You may find a `whereExists` method performs better for you than a `whereIn` with a subquery.
{% endhint %}

### whereNotIn

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| column | string \| Expression | `true` |  | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query. |
| values | string \| array \| [Expression](raw-expressions.md) \| Function \| QueryBuilder | `true` |  | A single value, list of values, or array of values to constrain a column with.  [`Expressions`](raw-expressions.md) may be used in any place a value is used.  Alternatively, a function or QueryBuilder instance can be passed in to be used as a subquery expression. |
| combinator | string | `false` | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods ](wheres.md#andwhere-and-orwhere)instead. |

Adds a where not in clause to the query.  This behaves identically to the `whereIn` method with the `negate`flag set to `true`.  See the documentation for [`whereIn`](wheres.md#wherein) for usage and examples.

### whereRaw

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| sql | string | `true` |  | The raw SQL to add to the query. |
| whereBindings | array | `false` | `[]` | Any bindings needed for the raw SQL.  Bindings can be simple values or [custom parameters](parameters-and-bindings.md#custom-parameter-types). |
| combinator | string | `false` | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods ](wheres.md#andwhere-and-orwhere)instead. |

Shorthand to add a raw SQL statement to the where clauses.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereRaw(
        "id = ? OR email = ? OR is_admin = 1",
        [ 1, "foo" ]
    );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE id = ? OR email = ? OR is_admin = 1
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### whereNull

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| column | string \| Expression | `true` |  | The name of the column to check if it is NULL.  Can also pass an [`Expression`](raw-expressions.md). |
| combinator | string | `false` | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods ](wheres.md#andwhere-and-orwhere)instead. |
| negate | boolean | `false` | `false` | False for NULL, True for NOT NULL. |

Adds a where null clause to the query.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereNull( "id" );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `id` IS NULL
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### whereNotNull

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| column | string \| Expression | `true` |  | The name of the column to check if it is NULL.  Can also pass an [`Expression`](raw-expressions.md). |
| combinator | string | `false` | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andWhere` and `orWhere` [dynamic methods ](wheres.md#andwhere-and-orwhere)instead. |
| negate | boolean | `false` | `false` | False for NULL, True for NOT NULL. |

Adds a where not in clause to the query.  This behaves identically to the [`whereNull`](wheres.md#wherenull) method with the `negate`flag set to `true`.  See the documentation for [`whereNull`](wheres.md#wherenull) for usage and examples.

## Dynamic Where Methods

qb uses `onMissingMethod` to provide a few different helpers when working with `where...` methods.

### andWhere... and orWhere...

Every `where...` method in qb can be called prefixed with either `and` or `or`.  Doing so will call the original method using the corresponding combinator.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "username", "like", "j%" )
    .andWhere( function( q ) {
        q.where( "isSubscribed", 1 )
            .orWhere( "isOnFreeTrial", 1 );
     } );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
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
{% endcode-tabs-item %}
{% endcode-tabs %}

### where{Column}

If you call a method starting with `where` that does not match an existing qb method, qb will instead call the `where` method using the rest of the method name as the first column name.  \(The rest of the arguments will be shifted to account for this.\)  This also applies to `andWhere{Column}` and `orWhere{Column}` method signatures.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereUsername( "like", "j%" )
    .whereActive( 1 );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `username` LIKE ?
  AND `active` = ?
```
{% endcode-tabs-item %}
{% endcode-tabs %}

