# Selects

## Specifying A Select Clause

You may not always want to select all columns from a database table. You can influence the select list of a query with the following methods.

Individual columns can contain fully-qualified names (`some_table.some_column`), table aliases (`alias.some_column`), and even set column aliases themselves (`some_column AS c`). The `columns` argument can be a single column, a list of columns (comma-separated), or an array of columns.

## select <a href="#get" id="get"></a>

| Name    | Type            | Required | Default | Description                                                        |
| ------- | --------------- | -------- | ------- | ------------------------------------------------------------------ |
| columns | string \| array | `false`  | ​`"*"`  | A single column, list of columns, or array of columns to retrieve. |

When calling `select` any previous columns are discarded. If you want to incrementally select columns, use the `addSelect` method.

If you pass no columns to this method, it will default to `"*"`.

{% code title="QueryBuilder" %}
```javascript
query.select( [ "fname AS firstName", "age" ] ).from( "users" );
```
{% endcode %}

{% code title="SQL (MySQL)" %}
```sql
SELECT `fname` AS `firstName`, `age` FROM `users`
```
{% endcode %}

## distinct <a href="#get" id="get"></a>

| Name  | Type    | Required | Default | Description                     |
| ----- | ------- | -------- | ------- | ------------------------------- |
| state | boolean | `false`  | ​`true` | Value to set the distinct flag. |

Calling distinct will cause the query to be executed with the `DISTINCT` keyword.

{% code title="QueryBuilder" %}
```javascript
query.select( "username" ).distinct().from( "users" );
```
{% endcode %}

{% code title="SQL (MySQL)" %}
```sql
SELECT DISTINCT `username` FROM `users`
```
{% endcode %}

{% hint style="info" %}
`distinct` applies to the entire query, not just certain fields.
{% endhint %}

## addSelect <a href="#get" id="get"></a>

| Name    | Type            | Required | Default | Description                                                                 |
| ------- | --------------- | -------- | ------- | --------------------------------------------------------------------------- |
| columns | string \| array | `true`   | ​       | A single column, list of columns, or array of columns to add to the select. |

This method adds the columns passed to it to the currently selected columns.

{% hint style="warning" %}
If the `QueryBuilder` is currently selecting all columns (`"*"`) when this method is called, the incoming columns will becoming the only columns selected.
{% endhint %}

{% code title="QueryBuilder" %}
```javascript
query.addSelect( [ "fname AS firstName", "age" ] ).from( "users" );
```
{% endcode %}

{% code title="SQL (MySQL)" %}
```sql
SELECT `fname` AS `firstName`, `age` FROM `users`
```
{% endcode %}

## selectRaw <a href="#get" id="get"></a>

| Name       | Type  | Required | Default | Description                                  |
| ---------- | ----- | -------- | ------- | -------------------------------------------- |
| expression | any   | `true`   | ​       | The raw expression for the select statement. |
| bindings   | array | `false`  | `[]`    | Any bindings needed for the raw expression.  |

A shortcut to use a raw expression in the select clause.

The expression is added to the other already selected columns.

_(To learn more about raw and expressions, check out the docs on_ [_Raw Expressions_](raw-expressions.md)_.)_

{% code title="QueryBuilder" %}
```javascript
query.selectRaw( "YEAR(birthdate) AS birth_year" ).from( "users" );
```
{% endcode %}

{% code title="SQL (MySQL)" %}
```sql
SELECT YEAR(birthdate) AS birth_year FROM `users`
```
{% endcode %}

## JSON Scalar Paths

Use `jsonPath` to select a scalar value from a JSON column without writing database-specific JSON syntax. Pass the JSON column, an array of object keys or array indexes, and an optional alias.

| Name   | Type   | Required | Default | Description                                                |
| ------ | ------ | -------- | ------- | ---------------------------------------------------------- |
| column | string | `true`   |         | The JSON column to traverse.                               |
| path   | array  | `false`  | `[]`    | Object keys and numeric array indexes to traverse.         |
| alias  | string | `false`  |         | An optional output alias when selecting the JSON value.    |

{% code title="Explicit Syntax" %}
```javascript
query
    .select( query.jsonPath(
        column = "profile",
        path = [ "contacts", 0, "email" ],
        alias = "email"
    ) )
    .from( "users" );
```
{% endcode %}

The arrow syntax is a shortcut for the same expression. The first segment is the relational column; following segments are the JSON path. Numeric segments address JSON array indexes.

{% code title="Arrow Shortcut" %}
```javascript
query
    .select( "profile->contacts->0->email AS email" )
    .from( "users" );
```
{% endcode %}

Both examples compile to the active grammar's scalar JSON expression. For example:

{% tabs %}
{% tab title="MySQL" %}
```sql
SELECT JSON_UNQUOTE(JSON_EXTRACT(`profile`, '$."contacts"[0]."email"')) AS `email`
FROM `users`
```
{% endtab %}

{% tab title="Postgres" %}
```sql
SELECT "profile"->'contacts'->0->>'email' AS "email"
FROM "users"
```
{% endtab %}

{% tab title="SQL Server" %}
```sql
SELECT JSON_VALUE([profile], '$."contacts"[0]."email"') AS [email]
FROM [users]
```
{% endtab %}
{% endtabs %}

JSON paths can also be used anywhere qb accepts a typed column, including `where`, `orderBy`, and related methods:

```javascript
query.from( "users" )
    .where( query.jsonPath( "profile", [ "age" ] ), ">=", 21 )
    .orderBy( "profile->name" );
```

{% hint style="info" %}
JSON query support is implemented by the MySQL, Postgres, SQL Server, Oracle, and SQLite grammars. Derby throws an `UnsupportedOperation` exception.
{% endhint %}

## subSelect <a href="#get" id="get"></a>

| Name  | Type                     | Required | Default | Description                                    |
| ----- | ------------------------ | -------- | ------- | ---------------------------------------------- |
| alias | string                   | `true`   | ​       | The alias for the subselect expression.        |
| query | Function \| QueryBuilder | `true`   |         | The callback or query to use in the subselect. |

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

{% code title="SQL (MySQL)" %}
```sql
SELECT (
    SELECT MAX(created_date)
    FROM `logins`
    WHERE `users`.`id` = `logins`.`user_id`
) AS `last_login_date`
FROM `users
```
{% endcode %}

## clearSelect <a href="#clearselect" id="clearselect"></a>

| Name         | Type | Required | Default | Description |
| ------------ | ---- | -------- | ------- | ----------- |
| No arguments |      | \`\`     |         |             |

Clears out the selected columns for a query along with any configured select bindings.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .select( [ "fname AS firstName", "age" ] )
    .clearSelect();
```
{% endcode %}

{% code title="SQL (MySQL)" %}
```sql
SELECT * FROM `users`
```
{% endcode %}

## reselect <a href="#reselect" id="reselect"></a>

| Name    | Type            | Required | Default | Description                                                        |
| ------- | --------------- | -------- | ------- | ------------------------------------------------------------------ |
| columns | string \| array | `false`  | ​`"*"`  | A single column, list of columns, or array of columns to retrieve. |

Clears out the selected columns for a query along with any configured select bindings. Then sets a selection of columns to select from the query. Any valid argument to [`select`](selects.md#get) can be passed here.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .select( [ "fname AS firstName", "age" ] )
    .reselect( "username" );
```
{% endcode %}

{% code title="SQL (MySQL)" %}
```sql
SELECT `username` FROM `users`
```
{% endcode %}

## reselectRaw <a href="#reselectraw" id="reselectraw"></a>

| Name       | Type  | Required | Default | Description                                  |
| ---------- | ----- | -------- | ------- | -------------------------------------------- |
| expression | any   | `true`   | ​       | The raw expression for the select statement. |
| bindings   | array | `false`  | `[]`    | Any bindings needed for the raw expression.  |

Clears out the selected columns for a query along with any configured select bindings. Then adds an Expression or array of expressions to the already selected columns.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .select( [ "fname AS firstName", "age" ] )
    .reselectRaw( "YEAR(birthdate) AS birth_year" );
```
{% endcode %}

{% code title="SQL (MySQL)" %}
```sql
SELECT YEAR(birthdate) AS birth_year FROM `users`
```
{% endcode %}

## Duplicate Select Column Validation

CFML query column names are case-insensitive. Selecting the same output name more than once can silently collapse columns in the returned query. qb can detect output names that are known when the query is compiled.

Enable the check during development with the `validateDuplicateSelectColumns` [module setting](../../installation-and-usage.md#configuration-settings):

```javascript
moduleSettings = {
    qb = {
        validateDuplicateSelectColumns = true
    }
};
```

```javascript
query
    .select( [ "users.id", "orders.id" ] )
    .from( "users" )
    .join( "orders", "orders.userId", "users.id" )
    .get();
// throws DuplicateSelectColumn
```

Alias duplicate output names when both columns are needed:

```javascript
query.select( [ "users.id AS userId", "orders.id AS orderId" ] );
```

The validation runs against the final select list when the query is compiled. It can identify simple columns, explicit aliases, subselect aliases, and explicitly aliased typed columns such as `jsonPath`. Wildcards and raw expressions without aliases are skipped because their output names are not known until the database executes the query.

{% hint style="info" %}
This validation is opt in and disabled by default. Enable it in development to catch mistakes early and leave it disabled in production to avoid the additional validation work.
{% endhint %}
