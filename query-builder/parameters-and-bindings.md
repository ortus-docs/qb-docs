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

