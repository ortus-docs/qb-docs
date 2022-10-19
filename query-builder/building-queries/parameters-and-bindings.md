# Query Parameters and Bindings

## Custom Parameter Types

When passing a parameter to qb, it will infer the sql type to be used.  If you pass a number, `CF_SQL_NUMERIC` will be used. If it is a date, `CF_SQL_TIMESTAMP`, and so forth. If you need more control, you can pass a struct with the parameters you would pass to [`cfqueryparam`](https://cfdocs.org/cfqueryparam).

{% hint style="success" %}
You can pass include any parameters you would use with [`cfqueryparam`](https://cfdocs.org/cfqueryparam) including `null`, `list`, etc.  This applies anywhere parameters are used including `where`, `update`, and `insert` methods.
{% endhint %}

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "id", "=", { value = 18, cfsqltype = "CF_SQL_VARCHAR" } );
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
        "id" = { value 1, cfsqltype = "CF_SQL_VARCHAR" },
        "age" = 18,
        "updatedDate" = { value = now(), cfsqltype = "CF_SQL_DATE" }
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

### Strict Date Detection

By default, qb will try to determine if a variable is a date using the built-in `isDate` function.  This can have some interesting effects with different formatted strings.  You can opt in to stricter date detection which will check the underlying Java class of the value to determine if the value is a date.  This is more accurate, but does require you to specifically pass date instances instead of strings.  For this reason, it is currently opt-in to not break existing applications.  It is likely to become the default in the next major version of qb.

You can opt in to stricter date detection by setting `strictDateDetection = true` in your `moduleSettings` in `config/ColdBox.cfc`.

```javascript
moduleSettings = {
    "qb": {
        "strictDateDetection": true
    }
};
```

### Numeric SQL Type

By default, qb will use the `CF_SQL_NUMERIC` SQL type when it detects a numeric binding.  You can specify your own default SQL type to use with numeric values using the `numericSQLType` setting in your `moduleSettings` in `config/ColdBox.cfc`.

```javascript
moduleSettings = {
    "qb": {
        "numericSQLType": "CF_SQL_INTEGER"
    }
};
```

### Automatic Scale Detection

In some combinations of database grammars and CFML engines, the `scale` argument on a `cfqueryparam` would default to `0`.  This would cause issues when attempting to insert a floating point number, even when using the correct SQL type (i.e., `CF_SQL_DECIMAL`) .  In 8.5.0, qb now automatically calculates a scale based on the value provided if the value is a floating point number.  This can be disabled by setting `autoAddScale` in your ColdBox config or passing `autoAddScale = false` when instantiating your `QueryBuilder` instance.

## Bindings

Bindings are the values that will be sent as parameters to a prepared SQL statement.  This protects you from [SQL injection.](https://en.wikipedia.org/wiki/SQL\_injection)  In CFML, this uses [`cfqueryparam`](https://cfdocs.org/cfqueryparam) to parameterize the values.

If you need to inspect the bindings for the current query you can retrieve them in order using the `getBindings` method.

{% hint style="info" %}
You can view the current SQL for the query with bindings inline for debugging purposes using the [`toSQL`](../debugging.md#tosql) method.
{% endhint %}

{% hint style="danger" %}
&#x20;Use these methods only for debugging. Modifying the bindings directly will likely cause issues when executing your query.  Adding or removing bindings should be done using the public API.
{% endhint %}

### getBindings

| Name         | Type | Required | Default | Description |
| ------------ | ---- | -------- | ------- | ----------- |
| No arguments |      | ``       |         |             |

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
    { value = "01 May 2019", cfsqltype = "CF_SQL_TIMESTAMP"  },
    { value = 1, cfsqltype = "CF_SQL_NUMERIC" }
]
```
{% endcode %}

You can also retrieve the bindings associated to their corresponding types.

### getRawBindings

| Name         | Type | Required | Default | Description |
| ------------ | ---- | -------- | ------- | ----------- |
| No arguments |      | ``       |         |             |

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
