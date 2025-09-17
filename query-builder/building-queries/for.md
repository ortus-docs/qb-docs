# For

{% hint style="warning" %}
This section only applies to SQL Server Grammars.
{% endhint %}

In SQL Server, `FOR` clauses are how you can return JSON or XML directly from your query.

In qb, only raw expressions are accepted via the `forRaw` method.

## forRaw

<table><thead><tr><th width="147.90625">Name</th><th width="121.19921875">Type</th><th width="80.03125">Required</th><th width="89.56640625">Default</th><th>Description</th></tr></thead><tbody><tr><td>expression</td><td>string</td><td>true</td><td></td><td>The raw sql for the <code>FOR</code> clause.</td></tr></tbody></table>



{% code title="QueryBuilder" %}
```javascript
query
    .select( [ "id", "name" ] )
    .from( "users" )
    .forRaw( "JSON AUTO" );
```
{% endcode %}

{% code title="SQL Server" %}
```sql
SELECT [id], [name]
FROM [users]
FOR JSON AUTO
```
{% endcode %}

