# Inserts, Updates, and Deletes

## insert

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| values | struct \| array&lt;struct&gt; | `true` |  | A struct or array of structs to insert in to the table. |
| options | struct | `false` | `{}` | Any additional `queryExecute` options. |
| toSQL | boolean | `false` | `false` | If `true`, returns the raw SQL string instead of running the query. Useful for debugging. |

{% hint style="warning" %}
This call must come after setting the query's table using [`from`](../building-queries/from.md#get) or [`table`](../building-queries/from.md#get-1).
{% endhint %}

You can insert a single record by passing a struct:

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .insert( {
        "name" = "Robert",
        "email" = "robert@test.com",
        "age" = 55
    } );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
INSERT INTO `users` (`age`, `email`, `name`)
VALUES (?, ?, ?)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

You can specify any [query param](../building-queries/parameters-and-bindings.md#custom-parameter-types) options such as the SQL type by passing a struct with the parameters you would pass to [`cfqueryparam`](https://cfdocs.org/cfqueryparam).

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .insert( {
        "name" = "Robert",
        "email" = "robert@test.com",
        "age" = { value = 55, cfsqltype = "CF_SQL_INTEGER" }
    } );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
INSERT INTO `users` (`age`, `email`, `name`)
VALUES (?, ?, ?)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Raw values can be supplied to an insert statement.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .insert( {
        "name" = "Robert",
        "email" = "robert@test.com",
        "updatedDate" = query.raw( "NOW()" )
    } );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
INSERT INTO `users` (`age`, `email`, `updatedDate`)
VALUES (?, ?, NOW())
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Multiple rows can be inserted in a batch by passing an array of structs to `insert`.

{% hint style="info" %}
This is not the same as looping over and array and calling `insert` in the loop.  Using an array with `insert` will batch the inserts in one SQL call.  Looping over an array and calling `insert` each time will create a SQL request for each item in the array.  Bottom line, pass your array to `insert`!
{% endhint %}

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" ).insert( [
    { "email" = "john@example.com", "name" = "John Doe" },
    { "email" = "jane@example.com", "name" = "Jane Doe" }
] );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
INSERT INTO `users` (`email`, `name`)
VALUES (?, ?), (?, ?)
```
{% endcode-tabs-item %}

{% code-tabs-item title="Oracle" %}
```sql
INSERT ALL
INTO "USERS" ("EMAIL", "NAME") VALUES (?, ?)
INTO "USERS" ("EMAIL", "NAME") VALUES (?, ?)
SELECT 1 FROM dual
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## returning

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| columns | string \| array | `true` |  | A single column, a list or columns, or an array of columns to return from the inserted query. |

{% hint style="danger" %}
`returning` is only supported in `PostgresGrammar` and `SqlServerGrammar`.  Using this method on unsupported grammars will result in an `UnsupportedOperation` exception.  Be aware that using this method constrains your grammar choices.
{% endhint %}

Specifies columns to be returned from the insert query.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .returning( "id" )
    .insert( {
        "email" = "foo",
        "name" = "bar"
    } );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="SQL Server" %}
```sql
INSERT INTO [users] ([email], [name])
OUTPUT INSERTED.[id]
VALUES (?, ?)
```
{% endcode-tabs-item %}

{% code-tabs-item title="Postgres" %}
```sql
INSERT INTO "users" ("email", "name")
VALUES (?, ?)
RETURNING "id"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## update

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| values | struct | `false` | `{}` | A struct of column and value pairs to update.  These column and value pairs are appended to any already set with the [`addUpdate`](inserts-updates-deletes.md#addupdate) method. |
| options | struct | `false` | `{}` | Any additional `queryExecute` options. |
| toSQL | boolean | `false` | `false` | If `true`, returns the raw SQL string instead of running the query. Useful for debugging. |

{% hint style="warning" %}
This call must come after setting the query's table using [`from`](../building-queries/from.md#get) or [`table`](../building-queries/from.md#get-1).
{% endhint %}

Updates a table with a struct of column and value pairs.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .update( {
        "email" = "foo",
        "name" = "bar"
    } );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
UPDATE `users`
SET `email` = ?,
    `name` = ?
```
{% endcode-tabs-item %}
{% endcode-tabs %}

You can specify any [query param](../building-queries/parameters-and-bindings.md#custom-parameter-types) options such as the SQL type by passing a struct with the parameters you would pass to [`cfqueryparam`](https://cfdocs.org/cfqueryparam).

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .update( {
        "email" = "foo",
        "name" = "bar",
        "updatedDate" = { value = now(), cfsqltype = "CF_SQL_TIMESTAMP" }
    } );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
UPDATE `users`
SET `email` = ?,
    `name` = ?,
    `updatedDate` = ?
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Any constraining of the update query should be done using the appropriate [WHERE](../building-queries/wheres.md) statement before calling `update`.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereId( 1 )
    .update( {
        "email" = "foo",
        "name" = "bar"
    } );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
UPDATE `users`
SET `email` = ?,
    `name` = ?
WHERE `Id` = ?
```
{% endcode-tabs-item %}
{% endcode-tabs %}

You can update a column based on another column using a raw expression.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "hits" )
    .where( "page", "someUrl" )
    .update( {
        "count" = query.raw( "count + 1" )
    } );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
UPDATE `hits`
SET `count` = count + 1
WHERE `page` = ?
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## addUpdate

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| values | struct | `true` |  | A struct of column and value pairs to add to the update clause. |

Adds values to a later [`update`](inserts-updates-deletes.md#update), similar to [`addSelect`](../building-queries/selects.md#get-2).

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereId( 1 )
    .addUpdate( {
        "email" = "foo",
        "name" = "bar"
    } )
    .when( true, function( q ) {
        q.addUpdate( {
            "foo": "yes"
        } );
    } )
    .when( false, function( q ) {
        q.addUpdate( {
            "bar": "no"
        } );
    } )
    .update();
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
UPDATE `users`
SET `email` = ?,
    `foo` = ?,
    `name` = ?
WHERE `Id` = ?
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## updateOrInsert

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| values | struct | `true` |  | A struct of column and value pairs to either update or insert. |
| options | boolean | `false` | `{}` | Any additional `queryExecute` options. |
| toSql | boolean | `false` | `false` | If `true`, returns the raw SQL string instead of running the query. Useful for debugging. |

Performs an update statement if the configured query returns `true` for `exists`.  Otherwise, performs an insert statement.

If an update statement is performed qb applies a `limit( 1 )` to the update statement.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "email", "foo" )
    .updateOrInsert( {
        "email" = "foo",
        "name" = "baz"
    } );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
UPDATE `users`
SET `email` = ?,
    `name` = ?
WHERE `email` = ?
LIMIT 1
```
{% endcode-tabs-item %}
{% endcode-tabs %}

If the configured query returns 0 records, then an insert statement is performed.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "email", "foo" )
    .updateOrInsert( {
        "email" = "foo",
        "name" = "baz"
    } );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
INSERT INTO `users` (`email`, `name`)
VALUES (?, ?)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## delete

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| id | any | `false` |  | A convenience argument for \`where\( "id", "=", arguments.id \). The query can be constrained by normal [WHERE](../building-queries/wheres.md) methods as well. |
| idColumn | string | `false` | `"id"` | The name of the id column for the delete shorthand. |
| options | boolean | `false` | `{}` | Any additional `queryExecute` options. |
| toSql | boolean | `false` | `false` | If `true`, returns the raw SQL string instead of running the query. Useful for debugging. |

Deletes all records that the query returns.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "email", "foo" )
    .delete();
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
DELETE FROM `users`
WHERE `email` = ?
```
{% endcode-tabs-item %}
{% endcode-tabs %}

The `id` argument is a convenience to delete a single record by id.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .delete( 1 );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
DELETE FROM `users`
WHERE `id` = ?
```
{% endcode-tabs-item %}
{% endcode-tabs %}

