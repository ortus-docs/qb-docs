# Raw Expressions

Raw expressions are the qb escape hatch.  While qb strives to provide ways to execute the majority of queries, you will occasionally need to provide raw sql values that are not processed by qb.  These SQL snippets are called `raw` or `Expressions` in qb.

{% hint style="warning" %}
`raw` expressions are useful, but should be used only if there is not another way to accomplish the same action using other qb methods.  This is because a `raw` expression has the potential to use syntax specific to one database grammar or another, preventing you from easily switching from one grammar to another, one of the major benefits of using qb.
{% endhint %}

The first way to retrieve an `Expression` is to call the `raw` method on the `QueryBuilder` object.

## raw

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| sql | string | true |  | The raw sql to wrap up in an Expression. |

The sql snippet passed to `raw` is not processed by qb at all.  With that in mind, it is important to follow all best practices and security recommendations with the sql you use with `raw`.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" ).select( query.raw( "MAX(created_date)" ) );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT MAX(created_date) FROM `users`

```
{% endcode %}

Expressions can be passed to most qb methods, like `select`, `from`, `where`, or `orderBy`, among others.  Additionally, qb provides some convenience methods to add raw values in different parts of the query:

* [selectRaw](selects.md#get-3)
* [fromRaw](from.md#get-2)
* [joinRaw](joins.md#joinraw)
* [leftJoinRaw](joins.md#leftjoinraw)
* [rightJoinRaw](joins.md#rightjoinraw)
* [crossJoinRaw](joins.md#crossjoinraw)
* [whereRaw](wheres.md#whereraw)

