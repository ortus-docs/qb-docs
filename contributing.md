# Contributing & Filing Issues

We welcome all types of contributions, following the [all-contributors](https://github.com/kentcdodds/all-contributors) specification. And please take a look at our wonderful contributors on the [README](/README.md)!

The most common type of contribution is to fix an incorrect SQL generation for a database grammar.

To debug what SQL is being ran, you can always call `toSQL` on any `QueryBuilder` or `SchemaBuilder` object.  Additionally, you can listen to the `preQBExecute` interception point for the generated SQL.

Each of the database grammars have two tests — `{Grammar}QueryBuilderSpec.cfc` and `{Grammar}SchemaBuilderSpec.cfc`.  These tests run the same qb syntax across the different grammars.  In each test are methods that return SQL strings like so:

```js
// MSSQLQueryBuilderSpec.cfc
function orWhere() {
    // If just a string is returned, we assume the bindings is an empty array ([])
    return {
        sql = "SELECT * FROM [users] WHERE [id] = ? OR [email] = ?",
        bindings = [ 1, "foo" ]
    };
}
```

```js
// OracleSchemaBuilderSpec.cfc
function boolean() {
    // returns an array since schema builder can execute multiple statements.
    return [ "CREATE TABLE ""USERS"" (""ACTIVE"" NUMBER(1, 0) NOT NULL)" ];
}
```

If you find an issue with the SQL generated from a grammar, please file a pull request with the correct SQL in these tests.  It's okay if you don't submit a fix as well. \(But we'd greatly appreciate it!\)  Doing so will help expedite the fix.

If you want to add support for a new database grammar, simply copy these two tests from an existing grammar, rename them, change the `getBuilder` method to return your new grammar, and fill out the SQL as it should be.  That will guide your implementation to be 100% compatible with the other grammars in qb.

