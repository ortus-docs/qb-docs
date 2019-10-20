# Group By and Having

## groupBy

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| groups | string \| array | `true` |  | A single column name, a list of column names, or an array of column names to group by.  An [`Expression`](raw-expressions.md) can be passed as well. |

Passing a single string will group by that one column.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .groupBy( "country" );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
GROUP BY `country`
```
{% endcode-tabs-item %}
{% endcode-tabs %}

You can also pass a list of column names.  A single comma \(`","`\) will be used as the delimiter.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .groupBy( "country,city" );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
GROUP BY `country`, `city`
```
{% endcode-tabs-item %}
{% endcode-tabs %}

An array of column names can be provided.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .groupBy( [ "country", "city" ] );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
GROUP BY `country`, `city`
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Calling `groupBy` multiple times will to the current groups.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .groupBy( "country" )
    .groupBy( "city" );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
GROUP BY `country`, `city`
```
{% endcode-tabs-item %}
{% endcode-tabs %}

An [`Expression`](raw-expressions.md) can be passed in place of a column.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .groupBy( query.raw( "DATE(created_at)" ) );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
GROUP BY DATE(created_at)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## having

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| column | string \| [Expression](raw-expressions.md) | `true` |  | The name of the column or [`Expression`](raw-expressions.md) with which to constrain the query. |
| operator | any | `false` |  | The operator to use for the constraint \(i.e. "=", "&lt;", "&gt;=", etc.\). A value can be passed as the `operator` and the `value` left null as a shortcut for equals \(e.g. where\( "column", 1 \) == where\( "column", "=", 1 \) \). |
| value | any | `false` |  | The value with which to constrain the column.  An [`Expression`](raw-expressions.md) can be passed as well. |
| combinator | string | `false` | `"and"` | The boolean combinator for the clause.  Valid options are `"and"` or `"or"`.  Avoid passing this parameter explicitly.  Where possible use the `andHaving` and `orHaving` methods instead. |

Adds a having clause to a query.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .groupBy( "email" )
    .having( "email", ">", 1 );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
GROUP BY `email`
HAVING `email` > ?
```
{% endcode-tabs-item %}
{% endcode-tabs %}

`Expressions` can be used in place of the column or the value.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .groupBy( "email" )
    .having( query.raw( "COUNT(email)" ), ">", 1 );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
GROUP BY `email`
HAVING COUNT(email) > ?
```
{% endcode-tabs-item %}
{% endcode-tabs %}

