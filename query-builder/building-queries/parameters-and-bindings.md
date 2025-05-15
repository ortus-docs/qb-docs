# Query Parameters and Bindings

## Custom Parameter Types

When passing a parameter to qb, it will infer the sql type to be used.  If you pass a number, `NUMERIC` will be used. If it is a date, `TIMESTAMP`, and so forth. If you need more control, you can pass a struct with the parameters you would pass to [`cfqueryparam`](https://cfdocs.org/cfqueryparam).

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

qb will use a different SQL type for integers and decimals.  You can customize the SQL types by setting the `integerSqlType` and `decimalSqlType` settings.

```cfscript
moduleSettings = {
    "qb": {
        "integerSqlType": "INTEGER",
        "decimalSqlType": "DECIMAL"
    }
};
```

Additionally, qb automatically calculates a scale based on the value provided if the value is a floating point number.

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

