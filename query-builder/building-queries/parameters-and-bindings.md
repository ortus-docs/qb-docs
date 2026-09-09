# Query Parameters and Bindings

## Custom Parameter Types

When passing a parameter to qb, it will infer the sql type to be used.  Numeric values use `INTEGER`, `BIGINT`, or `DECIMAL` by default, depending on their value. If it is a date, `TIMESTAMP`, and so forth. If you need more control, you can pass a struct with the parameters you would pass to [`cfqueryparam`](https://cfdocs.org/cfqueryparam).

{% hint style="success" %}
You can pass include any parameters you would use with [`cfqueryparam`](https://cfdocs.org/cfqueryparam) including `null`, `list`, etc.  This applies anywhere parameters are used including `where`, `update`, and `insert` methods.
{% endhint %}

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "id", "=", { value = 18, cfsqltype = "VARCHAR" } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `id` = ?
```
{% endcode %}

This can be used when inserting or updating records as well.

{% code title="QueryBuilder" %}
```javascript
query.table( "users" )
    .insert( {
        "id" = { value 1, cfsqltype = "VARCHAR" },
        "age" = 18,
        "updatedDate" = { value = now(), cfsqltype = "DATE" }
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
INSERT INTO `users`
    (`id`, `age`, `updatedDate`)
VALUES
    (?, ?, ?)
```
{% endcode %}

### Numeric SQL Types

qb uses separate types for signed 32-bit whole numbers, larger whole numbers, and values with a decimal portion. You can customize them with the `integerSQLType`, `bigIntegerSQLType`, and `decimalSQLType` settings. See [SQL Type Inference](../../installation-and-usage.md#sql-type-inference) for the boundaries.

```cfscript
moduleSettings = {
    "qb": {
        "integerSQLType": "INTEGER",
        "bigIntegerSQLType": "BIGINT",
        "decimalSQLType": "DECIMAL"
    }
};
```

qb automatically calculates a scale for decimal bindings when one is not supplied. For an array binding, it uses the greatest scale among its members.

### Numeric Lists

When an array must share one binding type, qb chooses a common numeric SQL type that covers its members. It considers declared SQL types, not just whether the current values happen to fit into a smaller type. Member order does not affect the result, and null members are ignored.

```cfscript
var utils = wirebox.getInstance( "QueryUtils@qb" );
var grammar = wirebox.getInstance( "MySQLGrammar@qb" );
var binding = utils.extractBinding(
    { value: [ 1, javacast( "long", "3000000000" ) ], list: true },
    grammar
);
// binding.cfsqltype is BIGINT
```

The cast ensures the large value is numeric on engines that parse large integer literals as strings. Numeric-looking strings retain their string type.

| Member types | Common type |
| --- | --- |
| `TINYINT` and `SMALLINT` | `SMALLINT` |
| `INTEGER` and `BIGINT` | `BIGINT` |
| `INTEGER` or `BIGINT` and `DECIMAL` | `DECIMAL` |
| `INTEGER` and `REAL` | `DOUBLE` |
| `INTEGER` and explicitly declared `FLOAT` | `FLOAT` |
| `REAL` and `DOUBLE` | `DOUBLE` |
| `BIGINT` and `DOUBLE` | `VARCHAR`, or an exception in strict mode |
| `DECIMAL` and `FLOAT` | `VARCHAR`, or an exception in strict mode |

Explicit member `cfsqltype` and `sqltype` values participate in promotion, including `cf_sql_` aliases. Supported numeric types include `BIT`, `TINYINT`, `SMALLINT`, `INTEGER`, `BIGINT`, `MONEY4`, `MONEY`, `DECIMAL`, `NUMERIC`, `REAL`, `FLOAT`, and `DOUBLE`. Java float and double values without an explicit SQL type first use qb's normal value inference; fractional values infer `DECIMAL` by default.

A larger magnitude range does not guarantee enough precision. qb conservatively treats exact decimal or money types mixed with approximate types, and `BIGINT` mixed with approximate types, as unsafe. The default is `VARCHAR`.

{% hint style="info" %}
We recommend enabling `throwOnUnsafeNumericInference` in development to catch potentially lossy numeric combinations early:

```cfscript
// In your development environment configuration:
moduleSettings.qb.throwOnUnsafeNumericInference = true;
```

The setting defaults to `false`. When enabled, unsafe numeric array inference throws `QBUnsafeNumericInference` and identifies the conflicting types. Safe promotions and ordinary mixed text arrays keep their normal behavior. Standalone callers can pass `throwOnUnsafeNumericInference = true` to the `QueryUtils` constructor.
{% endhint %}

An explicit `cfsqltype` or `sqltype` on the outer binding takes precedence over inference. The setting does not validate caller-selected conversions or the destination column's precision and scale. Falling back to `VARCHAR` preserves the binding representation, but the database may still convert it during query execution.

## Bindings

Bindings are the values that will be sent as parameters to a prepared SQL statement.  This protects you from [SQL injection.](https://en.wikipedia.org/wiki/SQL_injection)  In CFML, this uses [`cfqueryparam`](https://cfdocs.org/cfqueryparam) to parameterize the values.

If you need to inspect the bindings for the current query you can retrieve them in order using the `getBindings` method.

{% hint style="info" %}
You can view the current SQL for the query with bindings inline for debugging purposes using the [`toSQL`](../debugging/#tosql) method.
{% endhint %}

{% hint style="danger" %}
&#x20;Use these methods only for debugging. Modifying the bindings directly will likely cause issues when executing your query.  Adding or removing bindings should be done using the public API.
{% endhint %}

### getBindings

| Name         | Type | Required | Default | Description |
| ------------ | ---- | -------- | ------- | ----------- |
| No arguments |      |          |         |             |

This method returns the current bindings in order to be used for the query.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .join( "logins", function( j ) {
        j.on( "users.id", "logins.user_id" );
        j.where( "logins.created_date", ">", dateAdd( "m", -1, "01 Jun 2019" ) );
    } )
    .where( "active", 1 );
```
{% endcode %}

{% code title="Result" %}
```sql
[
    { value = "01 May 2019", cfsqltype = "TIMESTAMP"  },
    { value = 1, cfsqltype = "INTEGER" }
]
```
{% endcode %}

You can also retrieve the bindings associated to their corresponding types.

### getRawBindings

| Name         | Type | Required | Default | Description |
| ------------ | ---- | -------- | ------- | ----------- |
| No arguments |      |          |         |             |

This method returns the current bindings  to be used for the query associated to their corresponding types.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .join( "logins", function( j ) {
        j.on( "users.id", "logins.user_id" );
        j.where( "logins.created_date", ">", dateAdd( "m", -1, "01 Jun 2019" ) );
    } )
    .where( "active", 1 );
```
{% endcode %}

{% code title="Result" %}
```sql
{
    "commonTables" = [],
    "select" = [],
    "join" = [
        { value = "01 May 2019", cfsqltype = "CF_SQL_TIMESTAMP"  },
    ],
    "where" = [
        { value = 1, cfsqltype = "CF_SQL_NUMERIC" }
    ],
    "union" = [],
    "insert" = [],
    "insertRaw" = [],
    "update" = []
};
```
{% endcode %}

### addBindings

Adds a single binding or an array of bindings to a query for a given type.

| Name        | Type                        | Required | Default   | Description                                                       |
| ----------- | --------------------------- | -------- | --------- | ----------------------------------------------------------------- |
| newBindings | `Struct` \| `Array<Struct>` | true     |           | A single binding or an array of bindings to add for a given type. |
| type        | `String`                    | false    | `"where"` | The type of binding to add.                                       |

### addBindingsFromBuilder

Adds all of the bindings from another builder instance.

| Name | Type           | Required | Default | Description                                                |
| ---- | -------------- | -------- | ------- | ---------------------------------------------------------- |
| qb   | `QueryBuilder` | true     |         | Another builder instance to copy all of the bindings from. |

