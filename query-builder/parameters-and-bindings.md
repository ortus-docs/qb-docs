# Parameters and Bindings

## Custom Parameter Types

When passing a parameter to qb, it will infer the sql type to be used.  If you pass a number, `CF_SQL_NUMERIC` will be used. If it is a date, `CF_SQL_TIMESTAMP`, and so forth. If you need more control, you can pass a struct with the parameters you would pass to [`cfqueryparam`](https://cfdocs.org/cfqueryparam).

{% hint style="success" %}
You can pass include any parameters you would use with [`cfqueryparam`](https://cfdocs.org/cfqueryparam) including `null`, `list`, etc.  This applies anywhere parameters are used including `where`, `update`, and `insert` methods.
{% endhint %}

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "id", "=", { value = 18, cfsqltype = "CF_SQL_VARCHAR" } );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `id` = ?
```
{% endcode-tabs-item %}
{% endcode-tabs %}

This can be used when inserting or updating records as well.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.table( "users" )
    .insert( {
        "id" = { value 1, cfsqltype = "CF_SQL_VARCHAR" },
        "age" = 18,
        "updatedDate" = { value = now(), cfsqltype = "CF_SQL_DATE" }
    } );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
INSERT INTO `users`
    (`id`, `age`, `updatedDate`)
VALUES
    (?, ?, ?)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Bindings

Bindings are the values that will be sent as parameters to a prepared SQL statement.  This protects you from [SQL injection.](https://en.wikipedia.org/wiki/SQL_injection)  In CFML, this uses [`cfqueryparam`](https://cfdocs.org/cfqueryparam) to parameterize the values.

If you need to inspect the bindings for the current query you can retrieve them in order using the `getBindings` method.

{% hint style="danger" %}
 Use this method only for debugging. Modifying the bindings directly will likely cause issues when executing your query.  Adding or removing bindings should be done using the public API.
{% endhint %}

### getBindings

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| No arguments |  | \`\` |  |  |

This method returns the current bindings in order to be used for the query.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .join( "logins", function( j ) {
        j.on( "users.id", "logins.user_id" );
        j.where( "logins.created_date", ">", dateAdd( "m", -1, "01 Jun 2019" ) );
    } )
    .where( "active", 1 );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="Result" %}
```sql
[
    { value = "01 May 2019", cfsqltype = "CF_SQL_TIMESTAMP"  },
    { value = 1, cfsqltype = "CF_SQL_NUMERIC" }
]
```
{% endcode-tabs-item %}
{% endcode-tabs %}

You can also retrieve the bindings associated to their corresponding types.

### getRawBindings

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| No arguments |  | \`\` |  |  |

This method returns the current bindings  to be used for the query associated to their corresponding types.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .join( "logins", function( j ) {
        j.on( "users.id", "logins.user_id" );
        j.where( "logins.created_date", ">", dateAdd( "m", -1, "01 Jun 2019" ) );
    } )
    .where( "active", 1 );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="Result" %}
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
{% endcode-tabs-item %}
{% endcode-tabs %}

