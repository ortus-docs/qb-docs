# Aggregates

The query builder also provides a variety of aggregate methods such as `count`, `max`, `min`, and `sum`. These methods take the headache out of setting up these common aggregate functions.

When executing any of the aggregate functions, any `where` restrictions on your query will still be applied.

Instead of returning a query, these methods return a simple value.

## exists

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| options | struct | `false` | `{}` | Any additional `queryExecute` options. |

Returns `true` if the query returns any rows.  Returns `false` otherwise.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" ).where( "username", "like", "jon%" ).exists();
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="SQL \(MySQL\)" %}
```sql
SELECT COUNT(*) AS aggregate FROM `users` WHERE `username` LIKE 'jon%'
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## count

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| column | string | `false` | `"*"` | The column on which to count records. |
| options | struct | `false` | `{}` | Any additional `queryExecute` options. |

Returns an integer number of rows returned by the query.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" ).count();
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="SQL \(MySQL\)" %}
```sql
SELECT COUNT(*) AS aggregate FROM `users`
```
{% endcode-tabs-item %}

{% code-tabs-item title="SQL Server" %}
```sql
SELECT COUNT(*) FROM [users]
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## max

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| column | string | `true` |  | The column on which to find the max. |
| options | struct | `false` | `{}` | Any additional `queryExecute` options. |

Returns the maximum value for the given column.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" ).max( "age" );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="SQL \(MySQL\)" %}
```sql
SELECT MAX(age) AS aggregate FROM `users`
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## min

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| column | string | `true` |  | The column on which to find the min. |
| options | struct | `false` | `{}` | Any additional `queryExecute` options. |

Returns the minimum value for the given column.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" ).min( "age" );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="SQL \(MySQL\)" %}
```sql
SELECT MIN(age) AS aggregate FROM `users`
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## sum

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| column | string | `true` |  | The column to sum. |
| options | struct | `false` | `{}` | Any additional `queryExecute` options. |

Returns the sum of all returned rows for the given column.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "employees" ).sum( "salary" );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="SQL \(MySQL\)" %}
```sql
SELECT SUM(salary) AS aggregate FROM `employees`
```
{% endcode-tabs-item %}
{% endcode-tabs %}

