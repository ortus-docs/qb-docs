# From

## from <a href="#from" id="from"></a>

| Name | Type                 | Required | Default | Description                                                                 |
| ---- | -------------------- | -------- | ------- | --------------------------------------------------------------------------- |
| from | string \| Expression | `true`   | ​       | The name of the table or a Expression object from which the query is based. |

Used to set the base table for the query.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT * FROM `users`
```
{% endcode %}

You can optionally specify an alias for the table.

{% code title="QueryBuilder" %}
```javascript
query.from( "users as u" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT * FROM `users` AS `u`
```
{% endcode %}

## table <a href="#table" id="table"></a>

| Name  | Type                 | Required | Default | Description                                                                 |
| ----- | -------------------- | -------- | ------- | --------------------------------------------------------------------------- |
| table | string \| Expression | `true`   | ​       | The name of the table or a Expression object from which the query is based. |

An alias for `from` where you like how calling `table` looks.

{% code title="QueryBuilder" %}
```javascript
query.table( "users" ).insert( { "name" = "jon" } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
INSERT INTO `users` (`name`) VALUES (?)
```
{% endcode %}

## fromRaw <a href="#fromraw" id="fromraw"></a>

| Name     | Type   | Required | Default | Description                             |
| -------- | ------ | -------- | ------- | --------------------------------------- |
| from     | string | `true`   | ​       | The sql snippet to use as the table.    |
| bindings | array  | `false`  | `[]`    | Any bindings needed for the expression. |

Sometimes you need more control over your `from` clause in order to add grammar specific instructions, such as adding SQL Server table hints to your queries.

{% code title="QueryBuilder" %}
```javascript
query.fromRaw( "[users] u (nolock)" ).get();
```
{% endcode %}

{% code title="SQL Server" %}
```sql
SELECT * FROM [users] u (nolock) 
```
{% endcode %}

Since the `fromRaw()` takes your string verbatim, it's important that you make sure your SQL declaration is escaped properly. Failure to properly escape your table names may result in SQL errors.

{% hint style="warning" %}
Using `fromRaw` will most likely tie your code to a specific database, so think carefully before using the `fromRaw` method if you want your project to be database agnostic.
{% endhint %}

Many database engines allow you to define User Defined Functions. For example, SQL Server allows you to define UDFs that will return a table. In these type of cases, it may be necessary to bind parameters to your `from` clause.

You can bind parameters to the `fromRaw()` method by passing a secondary argument that is an array of the parameters to bind.

{% code title="QueryBuilder" %}
```javascript
query.fromRaw(
    "dbo.generateDateTable(?, ?, ?) as dt",
    [ "2017-01-01", "2017-12-31", "m" ]
).get();
```
{% endcode %}

{% code title="SQL Server" %}
```sql
SELECT * FROM dbo.generateDateTable(?, ?, ?) as dt
```
{% endcode %}

## fromSub <a href="#fromsub" id="fromsub"></a>

| Name  | Type                     | Required | Default | Description                                                                |
| ----- | ------------------------ | -------- | ------- | -------------------------------------------------------------------------- |
| alias | string                   | `true`   | ​       | The alias for the derived table.                                           |
| input | Function \| QueryBuilder | `true`   |         | Either a `QueryBuilder` instance or a closure to define the derived query. |

Complex queries often contain derived tables. Derived tables are essentially a temporal table defined as a subquery in the `from` statement.

{% code title="QueryBuilder" %}
```javascript
query.select( [ "firstName", "lastName" ] )
    .fromSub( "legalUsers", function ( q ) {
        q.select( [ "lName as lastName", "fName as firstName" ] )
            .from( "users" )
            .where( "age", ">=", 21 )
        ;
    } )
    .orderBy( "lastName" )
    .get()
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT `firstName`, `lastName`
FROM (
    SELECT `lName` as `lastName`, `fName` as `firstName`
    FROM `users`
    WHERE `age` >= 21
) AS `legalUsers`
ORDER BY `lastName`
```
{% endcode %}

In additional a function callback, a separate `QueryBuilder` instance can be passed to the `fromSub` method.

{% code title="QueryBuilder" %}
```javascript
var legalUsersQuery = query
    .select( [ "lName as lastName", "fName as firstName" ] )
    .from( "users" )
    .where( "age", ">=", 21 );

query.select( [ "firstName", "lastName" ] )
    .fromSub( "legalUsers", legalUsersQuery )
    .orderBy( "lastName" )
    .get();
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT `firstName`, `lastName`
FROM (
    SELECT `lName` as `lastName`, `fName` as `firstName`
    FROM `users`
    WHERE `age` >= 21
) AS `legalUsers`
ORDER BY `lastName`
```
{% endcode %}
