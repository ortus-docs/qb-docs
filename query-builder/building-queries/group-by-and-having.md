# Group By and Having

## groupBy

| Name   | Type            | Required | Default | Description                                                                                                                                          |
| ------ | --------------- | -------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| groups | string \| array | `true`   |         | A single column name, a list of column names, or an array of column names to group by.  An [`Expression`](raw-expressions.md) can be passed as well. |

Passing a single string will group by that one column.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .groupBy( "country" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
GROUP BY `country`
```
{% endcode %}

You can also pass a list of column names.  A single comma (`","`) will be used as the delimiter.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .groupBy( "country,city" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
GROUP BY `country`, `city`
```
{% endcode %}

An array of column names can be provided.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .groupBy( [ "country", "city" ] );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
GROUP BY `country`, `city`
```
{% endcode %}

Calling `groupBy` multiple times will to the current groups.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .groupBy( "country" )
    .groupBy( "city" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
GROUP BY `country`, `city`
```
{% endcode %}

An [`Expression`](raw-expressions.md) can be passed in place of a column.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .groupBy( query.raw( "DATE(created_at)" ) );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
GROUP BY DATE(created_at)
```
{% endcode %}

## having

| Name       | Type                                       | Required | Default | Description                                                                                                                                                                                                               |
| ---------- | ------------------------------------------ | -------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| column     | string \| [Expression](raw-expressions.md) | `true`   |         | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query.                                                                                                                           |
| operator   | any                                        | `false`  |         | The operator to use for the constraint (i.e. "=", "<", ">=", etc.). A value can be passed as the `operator` and the `value` left null as a shortcut for equals (e.g. where( "column", 1 ) == where( "column", "=", 1 ) ). |
| value      | any                                        | `false`  |         | The value with which to constrain the column.  An [`Expression`](raw-expressions.md) can be passed as well.                                                                                                               |
| combinator | string                                     | `false`  | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andHaving` and `orHaving` methods instead.                                |

Adds a having clause to a query.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .groupBy( "email" )
    .having( "email", ">", 1 );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
GROUP BY `email`
HAVING `email` > ?
```
{% endcode %}

`Expressions` can be used in place of the column or the value.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .groupBy( "email" )
    .having( query.raw( "COUNT(email)" ), ">", 1 );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
GROUP BY `email`
HAVING COUNT(email) > ?
```
{% endcode %}
