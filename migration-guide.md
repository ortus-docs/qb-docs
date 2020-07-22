# Migration Guide

## v8.0.0

### Where clauses with an OR combinator are now automatically wrapped inside [`when`](query-builder/building-queries/when.md#when) callbacks

This isn't a breaking change that will affect most people.  In fact, it will most likely improve your code.

Previously, when using the [`when`](query-builder/building-queries/when.md#when) control flow function, you were fully responsible for the wrapping of your where statements.  For example, the following query:

```javascript
qb.from( "users" )
    .where( "active", 1 )
    .when( len( url.q ), function( q ) {
        q.where( "username", "LIKE", q & "%" )
            .orWhere( "email", "LIKE", q & "%" );   
    } );
```

Would generate the following SQL:

```sql
SELECT *
FROM "users"
WHERE "active" = ?
    AND "username" = ?
    OR "email" = ?
```

The problem with this statement is that the `OR` can short circuit the `active` check.

The fix is to wrap the `LIKE` statements in parenthesis.  This is done in qb using a function callback to `where`.

```javascript
qb.from( "users" )
    .where( "active", 1 )
    .where( function( q ) {
        q.where( "username", "LIKE", q & "%" )
            .orWhere( "email", "LIKE", q & "%" );
    } );
```

```sql
SELECT *
FROM "users"
WHERE "active" = ?
    AND (
        "username" = ?
        OR "email" = ?
    )
```

When using the `when` control flow function, it was easy to miss this.  This is because you are already in a closure - it looks the same as when using `where` to group the clauses.

In qb 8.0.0, `when` will automatically group added where clauses when needed.  That means our original example now produces the SQL we probably expected.

```javascript
// qb 8.0.0
qb.from( "users" )
    .where( "active", 1 )
    .when( len( url.q ), function( q ) {
        q.where( "username", "LIKE", q & "%" )
            .orWhere( "email", "LIKE", q & "%" );   
    } );
```

```sql
SELECT *
FROM "users"
WHERE "active" = ?
    AND (
        "username" = ?
        OR "email" = ?
    )
```

Grouping is not needed if there is no `OR` combinator.  In these cases no grouping is added.

```javascript
// qb 8.0.0
qb.from( "users" )
    .where( "active", 1 )
    .when( url.keyExists( "admin" ), function( q ) {
        q.where( "admin", 1 )
            .whereNotNull( "hireDate" );
    } );
```

```sql
SELECT *
FROM "users"
WHERE "active" = ?
    AND "admin" = ?
    AND "hireDate IS NOT NULL
```

If you had already wrapped your expression in a group inside the `when` callback, nothing changes.  Your code works as before.  The `OR` combinator check only works on the top most level of added where clauses.

```javascript
qb.from( "users" )
    .where( "active", 1 )
    .when( len( url.q ), function( q ) {
        q.where( function( q2 ) {
            q2.where( "username", "LIKE", q & "%" )
                .orWhere( "email", "LIKE", q & "%" );
        } );
    } );
```

```sql
SELECT *
FROM "users"
WHERE "active" = ?
    AND (
        "username" = ?
        OR "email" = ?
    )
```

Additionally, if you do not add any where clauses inside a `when` callback, nothing changes from qb 7.

The breaking change part is if you were relying on these statements residing at the same level without grouping.  In those cases, you may pass the `withoutScoping` flag to the `when` callback.

```javascript
// qb 8.0.0
qb.from( "users" )
    .where( "active", 1 )
    .when(
        condition = len( url.q ),
        onTrue = function( q ) {
            q.where( "username", "LIKE", q & "%" )
                .orWhere( "email", "LIKE", q & "%" );   
        },
        withoutScoping = true
    );
```

```sql
SELECT *
FROM "users"
WHERE "active" = ?
    AND "username" = ?
    OR "email" = ?
```

## v7.0.0

### Lucee 4.5 and Adobe ColdFusion 11 EOL

Support for Lucee 4.5 and Adobe ColdFusion 11 has been dropped. If you need support for these engines, please remain on an earlier version of qb.

### MSSQLGrammar renamed to SqlServerGrammar

`MSSQLGrammar` was visually too close to `MySQLGrammar` and was hard to differentiate quickly. `SqlServerGrammar` is much more unique and easily identifiable. Additionally, more people that use this library refer to their database engine as "SQL Server" than "MSSQL".

To migrate, replace any instances of `MSSQLGrammar` with `SqlServerGrammar`. Make sure to also append the `@qb` namespace, if needed, [as explained below.](migration-guide.md#defaultgrammar-updated-to-be-the-full-wirebox-mapping)

### Variadic Parameters Support Removed

Variadic parameter support was the ability to pass any number of arguments to certain methods like `select`.

```javascript
qb.select( "name", "email", "createdDate" );
```

This code came with a slight performance cost and readability cost. That, combined with the fact that the above syntax is very close to an array, we are dropping support for variadic parameters. To migrate, wrap instances of variadic parameters in an array:

```javascript
qb.select( [ "name", "email", "createdDate" ] );
```

### defaultGrammar updated to be the full WireBox mapping

In previous versions, the value passed to `defaultGrammar` was used to look up a mapping in the `@qb` namespace. This made it difficult to add or use grammars that weren't part of qb. \(You could get around this be registering your custom grammar in the `@qb` namespace, but doing so seemed strange.\)

To migrate this code, change your `defaultGrammar` to be the full WireBox mapping in your `moduleSettings`:

```javascript
moduleSettings = {
    "qb": {
        "defaultGrammar": "MSSQLGrammar@qb"
    }
};
```

### value method argument order changed

A `defaultValue` parameter and optional exception throwing was added to `value`. This pushed the `options` struct to the end of the method. If you are using positional parameters with `value`, you will need to update your method calls to either use named parameters or the new positions.

```javascript
public any function value(
    required string column,
    string defaultValue = "",
    boolean throwWhenNotFound = false,
    struct options = {}
);
```

### Some methods renamed `callback` to `query`

All methods that could conceivably take a subquery as well as a value now accept a closure or another builder instance to use as a subquery. This led to changing the `callback` argument to `query` in the following cases:

* `whereSub`
* `whereInSub`
* `whereExists`
* `orWhereExists`
* `whereNotExists`
* `andWhereNotExists`
* `orWhereNotExists`
* `whereNullSub`
* `orderBySub`
* `subSelect`

If you are using named parameters with any of the above methods you will need to migrate your method calls.

## v5.0.0

Version `v5.0.0` brings support for `SchemaBuilder` inside `qb`. To avoid naming confusion, `Builder` was renamed to `QueryBuilder`. Any references in your code to `Builder@qb` need to be updated to `QueryBuilder@qb`.

