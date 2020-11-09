# Selects

## Specifying A Select Clause

You may not always want to select all columns from a database table. You can influence the select list of a query with the following methods.

Individual columns can contain fully-qualified names \(`some_table.some_column`\), table aliases \(`alias.some_column`\), and even set column aliases themselves \(`some_column AS c`\). The `columns` argument can be a single column, a list of columns \(comma-separated\), or an array of columns.

## select <a id="get"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| columns | string \| array | `false` | ​`"*"` | A single column, list of columns, or array of columns to retrieve. |

When calling `select` any previous columns are discarded. If you want to incrementally select columns, use the `addSelect` method.

If you pass no columns to this method, it will default to `"*"`.

{% code title="QueryBuilder" %}
```javascript
query.select( [ "fname AS firstName", "age" ] ).from( "users" );
```
{% endcode %}

{% code title="SQL \(MySQL\)" %}
```sql
SELECT `fname` AS `firstName`, `age` FROM `users`
```
{% endcode %}

## distinct <a id="get"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| state | boolean | `false` | ​`true` | Value to set the distinct flag. |

Calling distinct will cause the query to be executed with the `DISTINCT` keyword.

{% code title="QueryBuilder" %}
```javascript
query.select( "username" ).distinct().from( "users" );
```
{% endcode %}

{% code title="SQL \(MySQL\)" %}
```sql
SELECT DISTINCT `username` FROM `users`
```
{% endcode %}

{% hint style="info" %}
`distinct` applies to the entire query, not just certain fields.
{% endhint %}

## addSelect <a id="get"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| columns | string \| array | `true` | ​ | A single column, list of columns, or array of columns to add to the select. |

This method adds the columns passed to it to the currently selected columns.

{% hint style="warning" %}
If the `QueryBuilder` is currently selecting all columns \(`"*"`\) when this method is called, the incoming columns will becoming the only columns selected.
{% endhint %}

{% code title="QueryBuilder" %}
```javascript
query.addSelect( [ "fname AS firstName", "age" ] ).from( "users" );
```
{% endcode %}

{% code title="SQL \(MySQL\)" %}
```sql
SELECT `fname` AS `firstName`, `age` FROM `users`
```
{% endcode %}

## selectRaw <a id="get"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| expression | any | `true` | ​ | The raw expression for the select statement. |
| bindings | array | `false` | `[]` | Any bindings needed for the raw expression. |

A shortcut to use a raw expression in the select clause.

The expression is added to the other already selected columns.

_\(To learn more about raw and expressions, check out the docs on_ [_Raw Expressions_](raw-expressions.md)_.\)_

{% code title="QueryBuilder" %}
```javascript
query.selectRaw( "YEAR(birthdate) AS birth_year" ).from( "users" );
```
{% endcode %}

{% code title="SQL \(MySQL\)" %}
```sql
SELECT YEAR(birthdate) AS birth_year FROM `users`
```
{% endcode %}

## subSelect <a id="get"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| alias | string | `true` | ​ | The alias for the subselect expression. |
| query | Function \| QueryBuilder | `true` |  | The callback or query to use in the subselect. |

The method lets you pass either a callback or a `QueryBuilder` instance to be used as a subselect expression. If a callback is passed it will be passed a new query instance as the only parameter.

The subselect is added to the other already selected columns.

{% code title="QueryBuilder" %}
```javascript
query.subSelect( "last_login_date", function( q ) {
    q.selectRaw( "MAX(created_date)" )
        .from( "logins" )
        .whereColumn( "users.id", "logins.user_id" );
} ) ).from( "users" );
```
{% endcode %}

{% code title="SQL \(MySQL\)" %}
```sql
SELECT (
    SELECT MAX(created_date)
    FROM `logins`
    WHERE `users`.`id` = `logins`.`user_id`
) AS `last_login_date`
FROM `users
```
{% endcode %}

## clearSelect <a id="clearselect"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| No arguments |  | \`\` |  |  |

Clears out the selected columns for a query along with any configured select bindings.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .select( [ "fname AS firstName", "age" ] )
    .clearSelect();
```
{% endcode %}

{% code title="SQL \(MySQL\)" %}
```sql
SELECT * FROM `users`
```
{% endcode %}

## reselect <a id="reselect"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| columns | string \| array | `false` | ​`"*"` | A single column, list of columns, or array of columns to retrieve. |

Clears out the selected columns for a query along with any configured select bindings. Then sets a selection of columns to select from the query. Any valid argument to [`select`](selects.md#get) can be passed here.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .select( [ "fname AS firstName", "age" ] )
    .reselect( "username" );
```
{% endcode %}

{% code title="SQL \(MySQL\)" %}
```sql
SELECT `username` FROM `users`
```
{% endcode %}

## reselectRaw <a id="reselectraw"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| expression | any | `true` | ​ | The raw expression for the select statement. |
| bindings | array | `false` | `[]` | Any bindings needed for the raw expression. |

Clears out the selected columns for a query along with any configured select bindings. Then adds an Expression or array of expressions to the already selected columns.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .select( [ "fname AS firstName", "age" ] )
    .reselectRaw( "YEAR(birthdate) AS birth_year" );
```
{% endcode %}

{% code title="SQL \(MySQL\)" %}
```sql
SELECT YEAR(birthdate) AS birth_year FROM `users`
```
{% endcode %}

