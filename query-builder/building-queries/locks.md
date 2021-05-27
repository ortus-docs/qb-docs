# Locks

qb includes a few methods to help you lock certain rows when executing `select` statements.

{% hint style="warning" %}
**Note:** For locks to work properly, they must be nested inside a `transaction`.  qb does not handle any of the transaction lifecycle for you.
{% endhint %}

## sharedLock

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| No arguments |  |  |  |  |

A shared lock prevents the selected rows from being modified until your transaction is committed.

{% tabs %}
{% tab title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "id", 1 )
    .sharedLock();
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `id` = ?
LOCK IN SHARE MODE
```
{% endtab %}

{% tab title="SQL Server" %}
```sql
SELECT *
FROM [users] WITH (ROWLOCK,HOLDLOCK)
WHERE [id] = ?
```
{% endtab %}

{% tab title="Postgres" %}
```sql
SELECT *
FROM "users"
WHERE "id" = ?
FOR SHARE
```
{% endtab %}

{% tab title="Oracle" %}
```sql
LOCK TABLE "USERS"
IN SHARE MODE NOWAIT;

SELECT *
FROM "USERS"
WHERE "ID" = ?
```
{% endtab %}
{% endtabs %}

## lockForUpdate

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| No arguments |  |  |  |  |

A lock for update lock prevents the selected rows from being modified or selected with another shared lock until your transaction is committed.

The main difference between a `sharedLock` and `lockForUpdate` is that a `lockForUpdate` prevents other reads or selects as well as updates.

{% tabs %}
{% tab title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "id", 1 )
    .lockForUpdate();
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="MySQL" %}
```sql
SELECT *
FROM `users`
WHERE `id` = ?
FOR UPDATE
```
{% endtab %}

{% tab title="SQL Server" %}
```sql
SELECT *
FROM [users] WITH (ROWLOCK,UPDLOCK,HOLDLOCK)
WHERE [id] = ?
```
{% endtab %}

{% tab title="Postgres" %}
```sql
SELECT *
FROM "users"
WHERE "id" = ?
FOR UPDATE
```
{% endtab %}

{% tab title="Oracle" %}
```sql
LOCK TABLE "USERS"
IN ROW EXCLUSIVE MODE NOWAIT;

SELECT *
FROM "USERS"
WHERE "ID" = ?
```
{% endtab %}
{% endtabs %}

## noLock

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| No arguments |  |  |  |  |

`noLock` will instruct your grammar to ignore any shared locks when executing the query.

Currently this only makes a difference in SQL Server grammars.

{% tabs %}
{% tab title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "id", 1 )
    .noLock();
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="SQL Server" %}
```sql
SELECT *
FROM [users] WITH (NOLOCK)
WHERE [id] = ?
```
{% endtab %}
{% endtabs %}

## lock

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| value | string | `true` |  | The custom lock directive to add to the query. |

The `lock` method will allow you to add a custom lock directive to your query.  Think of it as the `raw` method for lock directives.

These lock directives vary from grammar to grammar.

## clearLock

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| No arguments |  |  |  |  |

Clears any lock directive on the query.

