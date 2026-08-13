# Unions

The query builder also lets you create union statements on your queries using either `UNION` or `UNION ALL` strategies.

The `union` methods take either a Query Builder instance or a closure which you use to define a new QueryBuilder instance.

Union statements are added in the order in which the `union` methods are invoked, but the `union` statements can be in any order in your API call stack. This means you can safely declare your `union` method calls before the `select`, `from` and `orderBy` calls on the source Query Builder instance.

* `union()` — This method builds a SQL statement using the `UNION` clause which combines two SQL queries into a single result set containing all the matching rows. The two queries _must_ have the same defined columns and compatible data types or the SQL engine will generate an error. The `union` clause only returns unique rows.
* `unionAll()` — This builds a SQL statement using the `UNION ALL` clause. This is the same as `union` but includes duplicate rows.&#x20;

{% hint style="danger" %}
**IMPORTANT:** In most grammars, QueryBuilder instances passed to a `union` statement _cannot_ contain a defined order. Any use of `orderBy()` on a unioned QueryBuilder instance results in an `OrderByNotAllowed` exception. To order the complete result, add `orderBy()` to the parent QueryBuilder instance.
{% endhint %}

The SQL Server grammar supports independently ordered union branches when the ordering determines a limited result using `TOP`, `OFFSET`, or `FETCH`. qb compiles those branches as derived tables so SQL Server applies the order and limit before performing the union.

```javascript
query.select( [ "id", "name", "modifiedDate" ] )
    .from( "pages" )
    .orderByDesc( "modifiedDate" )
    .limit( 5 )
    .unionAll( function( q ) {
        q.select( [ "id", "name", "modifiedDate" ] )
            .from( "documents" )
            .orderByDesc( "modifiedDate" )
            .limit( 5 );
    } );
```

```sql
SELECT * FROM (
    SELECT TOP (5) [id], [name], [modifiedDate]
    FROM [pages]
    ORDER BY [modifiedDate] DESC
) AS [qb_union_0]
UNION ALL
SELECT * FROM (
    SELECT TOP (5) [id], [name], [modifiedDate]
    FROM [documents]
    ORDER BY [modifiedDate] DESC
) AS [qb_union_1]
```

An ordered SQL Server union branch without a limit still throws `OrderByNotAllowed`.

## union

| Name  | Type                     | Required | Default | Description                                                                                                                               |
| ----- | ------------------------ | -------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| input | Function \| QueryBuilder | `true`   |         | The function or QueryBuilder instance to use as the unioned query.                                                                        |
| all   | boolean                  | `false`  | `false` | Determines if statement should be a "UNION ALL". Passing this as an argument is discouraged. Use the dedicated `unionAll` where possible. |

Adds a UNION statement to the query.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .select( "name" )
    .where( "id", 1 )
    .union( function ( q ) {
        q.from( "users" )
            .select( "name" )
            .where( "id", 2 );
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT `name`
FROM `users`
WHERE `id` = ?
UNION
SELECT `name`
FROM `users`
WHERE `id` = ?
```
{% endcode %}

Adding multiple union statements will append it to the query.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .select( "name" )
    .where( "id", 1 )
    .union( function ( q ) {
        q.from( "users" )
            .select( "name" )
            .where( "id", 2 );
    } )
    .union( function ( q ) {
        q.from( "users" )
            .select("name")
            .where( "id", 3 );
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT `name`
FROM `users`
WHERE `id` = ?
UNION
SELECT `name`
FROM `users`
WHERE `id` = ?
UNION
SELECT `name`
FROM `users`
WHERE `id` = ?
```
{% endcode %}

It can also add union queries as QueryBuilder instances.

{% code title="QueryBuilder" %}
```javascript
var q1 = query.newQuery()
    .from( "users" )
    .select( "name" )
    .where( "id", 2 );
    
var q2 = query.newQuery()
    .from( "users" )
    .select( "name" )
    .where( "id", 3 );

query.from( "users" )
    .select( "name" )
    .where( "id", 1 )
    .union( q1 )
    .union( q2 );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT `name`
FROM `users`
WHERE `id` = ?
UNION
SELECT `name`
FROM `users`
WHERE `id` = ?
UNION
SELECT `name`
FROM `users`
WHERE `id` = ?
```
{% endcode %}

## unionAll

| Name  | Type                     | Required | Default | Description                                                        |
| ----- | ------------------------ | -------- | ------- | ------------------------------------------------------------------ |
| input | Function \| QueryBuilder | `true`   |         | The function or QueryBuilder instance to use as the unioned query. |

Adds a UNION ALL statement to the query.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .select( "name" )
    .where( "id", 1 )
    .unionAll( function( q ) {
        q.from( "users" )
            .select( "name" )
            .where( "id", 2 );
     } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT `name`
FROM `users`
WHERE `id` = ?
UNION ALL
SELECT `name`
FROM `users`
WHERE `id` = ?
```
{% endcode %}

Adding multiple `unionAll` statements will append it to the query.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .select( "name" )
    .where( "id", 1 )
    .unionAll( function( q ) {
        q.from( "users" )
            .select( "name" )
            .where( "id", 2 );
     } )
    .unionAll( function( q ) {
        q.from( "users" )
            .select( "name" )
            .where( "id", 3 );
     } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT `name`
FROM `users`
WHERE `id` = ?
UNION ALL
SELECT `name`
FROM `users`
WHERE `id` = ?
UNION ALL
SELECT `name`
FROM `users`
WHERE `id` = ?
```
{% endcode %}

It can also add union queries as QueryBuilder instances.

{% code title="QueryBuilder" %}
```javascript
var q1 = query.newQuery()
    .from( "users" )
    .select( "name" )
    .where( "id", 2 );
    
var q2 = query.newQuery()
    .from( "users" )
    .select( "name" )
    .where( "id", 3 );

query.from( "users" )
    .select( "name" )
    .where( "id", 1 )
    .unionAll( q1 )
    .unionAll( q2 );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT `name`
FROM `users`
WHERE `id` = ?
UNION ALL
SELECT `name`
FROM `users`
WHERE `id` = ?
UNION ALL
SELECT `name`
FROM `users`
WHERE `id` = ?
```
{% endcode %}
