# Joins

Join clauses range from simple to complex including joining complete subqueries on multiple conditions. qb has your back with all of these use cases.

| Table of Contents |  |  |  |
| :--- | :--- | :--- | :--- |
| [join](joins.md#join) | [joinRaw](joins.md#joinraw) | [joinSub](joins.md#joinsub) | [joinWhere](joins.md#joinwhere) |
| [leftJoin](joins.md#leftjoin) | [leftJoinRaw](joins.md#leftjoinraw) | [leftJoinSub](joins.md#leftjoinsub) | [newJoin](joins.md#newjoin) |
| [rightJoin](joins.md#get) | [rightJoinRaw](joins.md#rightjoinraw) | [rightJoinSub](joins.md#rightjoinsub) | [JoinClause](joins.md#joinclause) |
| [crossJoin](joins.md#crossjoin) | [crossJoinRaw](joins.md#crossjoinraw) | [crossJoinSub](joins.md#crossjoinsub) |  |

## join <a id="join"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| table | string \| [Expression](raw-expressions.md) \| [JoinClause](joins.md#joinclause) | `true` | ​ | The name of the table or a [`Expression`](raw-expressions.md) object from which the query is based.  Alternatively, a configured [`JoinClause`](joins.md#joinclause) instance can be passed. |
| first | string \| [Expression](raw-expressions.md) \| Function | `false` |  | The first column or [`Expression`](raw-expressions.md) to join the table on.  Alternatively, a function can be passed to configure complex join statements. |
| operator | string | `false` | `"="` | The boolean operator for the join clause. |
| second | string \| [Expression](raw-expressions.md) | `false` |  | The second column or [`Expression`](raw-expressions.md) to join the table on. |
| type | string | `false` | `"inner"` | The type of the join.  Passing this as an argument is discouraged for readability. Use the dedicated methods like [`leftJoin`](joins.md#leftjoin) and [`rightJoin`](joins.md#get) where possible. |
| where | boolean | `false` | `false` | Sets if the value of `second` should be interpreted as a column or a value. Passing this as an argument is discouraged. Use the dedicated [`joinWhere`](joins.md#joinwhere) or a join closure where possible. |

Applies a join to the query. The simplest join is to a table based on two columns:

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .join( "posts", "users.id", "=", "posts.author_id" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
JOIN `posts`
  ON `users`.`id` = `posts`.`author_id`
```
{% endcode %}

When doing a simple join using `=` as the operator, you can omit it and pass just the column names:

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .join( "posts", "users.id", "posts.author_id" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
JOIN `posts`
  ON `users`.`id` = `posts`.`author_id`
```
{% endcode %}

\`\`[`Expressions`](raw-expressions.md) are also supported as the `table` argument \(though you may prefer the readability of the [`joinRaw`](joins.md#joinraw) method\):

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .join( query.raw( "posts (nolock)" ), "users.id", "=", "posts.author_id" );
```
{% endcode %}

{% code title="SQL Server" %}
```sql
SELECT *
FROM [users]
JOIN posts (nolock)
  ON [users].[id] = [posts].[author_id]
```
{% endcode %}

{% hint style="warning" %}
Using `raw` will most likely tie your code to a specific database, so think carefully before using the `raw` method if you want your project to be database agnostic.
{% endhint %}

When you need to specify more clauses to join, you can pass a function as the second argument:

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .join( "posts", function( j ) {
        j.on( "users.id", "=", "posts.author_id" );
        j.on( "users.prefix", "=", "posts.prefix" );
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
JOIN `posts`
  ON `users`.`id` = `posts`.`author_id`
  AND `users`.`prefix` = `posts`.`prefix`
```
{% endcode %}

You can specify [`where`](wheres.md) clauses in your joins as well.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .join( "posts", function( j ) {
        j.on( "users.id", "=", "posts.author_id" );
        j.whereNotNull( "posts.published_date" );
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
JOIN `posts`
  ON `users`.`id` = `posts`.`author_id`
  AND `posts`.`published_date` IS NOT NULL
```
{% endcode %}

Conditions inside a join clause can be grouped using a function.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .join( "posts", function( j ) {
        j.on( function( j1 ) {
            j1.on( "users.id", "posts.author_id" )
                .orOn( "users.id", "posts.reviewer_id" );
        } );
        j.whereNotNull( "posts.published_date" );
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
JOIN `posts`
  ON (
      `users`.`id` = `posts`.`author_id`
      OR `users`.`id` = `posts`.`reviewer_id`
  )
  AND `posts`.`published_date` IS NOT NULL
```
{% endcode %}

A preconfigured [`JoinClause`](joins.md#joinclause) can also be passed to the join function. This allows you to extract shared pieces of code out to different functions.

{% code title="QueryBuilder" %}
```javascript
var j = query.newJoin( "contacts" )
    .on( "users.id", "posts.author_id" );

query.from( "users" ).join( j );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
JOIN `posts`
  ON `users`.`id` = `posts`.`author_id`
```
{% endcode %}

## joinWhere <a id="joinwhere"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| table | string | `true` | ​ | The raw SQL string to use as the table. |
| first | string \| [Expression](raw-expressions.md) \| Function | `false` |  | The first column or [`Expression`](raw-expressions.md) to join the table on.  Alternatively, a function can be passed to configure complex join statements. |
| operator | string | `false` | `"="` | The boolean operator for the join clause. |
| second | string \| [Expression](raw-expressions.md) | `false` |  | The second column or [`Expression`](raw-expressions.md) to join the table on. |
| type | string | `false` | `"inner"` | The type of the join.  Passing this as an argument is discouraged for readability. Use the dedicated methods like [`leftJoin`](joins.md#leftjoin) and [`rightJoin`](joins.md#get) with a join function where possible. |

Adds a join to another table based on a `WHERE` clause instead of an `ON` clause. `WHERE` clauses introduce parameters and parameter bindings whereas `on` clauses join between columns and don't need parameter bindings.

For simple joins, this specifies a column on which to join the two tables:

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .joinWhere( "contacts", "contacts.balance", "<", 100 );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
JOIN `contacts`
  WHERE `contacts`.`balance` < ?
```
{% endcode %}

For complex joins, a function can be passed to `first`. This allows multiple `on` and `where` conditions to be applied to the join. See the documentation for [`join`](joins.md#join) for more information.

## joinRaw <a id="joinraw"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| table | string | `true` | ​ | The raw SQL string to use as the table. |
| first | string \| [Expression](raw-expressions.md) \| Function | `false` |  | The first column or [`Expression`](raw-expressions.md) to join the table on.  Alternatively, a function can be passed to configure complex join statements. |
| operator | string | `false` | `"="` | The boolean operator for the join clause. |
| second | string \| [Expression](raw-expressions.md) | `false` |  | The second column or [`Expression`](raw-expressions.md) to join the table on. |
| type | string | `false` | `"inner"` | The type of the join.  Passing this as an argument is discouraged for readability. Use the dedicated methods like [`leftJoinRaw`](joins.md#leftjoinraw) and [`rightJoinRaw`](joins.md#rightjoinraw) where possible. |
| where | boolean | `false` | `false` | Sets if the value of `second` should be interpreted as a column or a value. Passing this as an argument is discouraged. Use a closure to define the where clauses where possible. |

Uses the raw SQL provided to as the table for the join clause. All the other functionality of `joinRaw` matches the [`join`](joins.md#join) method. Additionally, there are [`leftJoinRaw`](joins.md#leftjoinraw), [`rightJoinRaw`](joins.md#rightjoinraw), and `crossJoinRaw` methods available.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .joinRaw( "posts (nolock)", "users.id", "posts.author_id" );
```
{% endcode %}

{% code title="SQL Server" %}
```sql
SELECT *
FROM [users]
JOIN posts (nolock)
  ON [users].[id] = [posts].[author_id]
```
{% endcode %}

{% hint style="warning" %}
Using `joinRaw` will most likely tie your code to a specific database, so think carefully before using the `joinRaw` method if you want your project to be database agnostic.
{% endhint %}

## joinSub <a id="joinsub"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| alias | string | `true` |  | The alias for the derived table. |
| input | Function \| QueryBuilder | `true` | ​ | Either a `QueryBuilder` instance or a function to define the derived query. |
| first | string \| [Expression](raw-expressions.md) \| Function | `true` |  | The first column or [`Expression`](raw-expressions.md) to join the table on.  Alternatively, a function can be passed to configure complex join statements. |
| operator | string | `false` | `"="` | The boolean operator for the join clause. |
| second | string \| [Expression](raw-expressions.md) | `false` |  | The second column or [`Expression`](raw-expressions.md) to join the table on. |
| type | string | `false` | `"inner"` | The type of the join.  Passing this as an argument is discouraged for readability. Use the dedicated methods like [`leftJoinSub`](joins.md#leftjoinsub) and [`rightJoinSub`](joins.md#rightjoinsub) where possible. |
| where | boolean | `false` | `false` | Sets if the value of `second` should be interpreted as a column or a value. Passing this as an argument is discouraged. Use a closure to define the where clauses where possible. |

Adds a join to a derived table. All the functionality of the [`join`](joins.md#join) method applies to constrain the query. The derived table can be defined using a `QueryBuilder` instance:

{% code title="QueryBuilder" %}
```javascript
var sub = query.newQuery()
    .select( "id" )
    .from( "contacts" )
    .whereNotIn( "id", [ 1, 2, 3 ] );

query.from( "users as u" )
    .joinSub( "c", sub, "u.id", "=", "c.id" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users` AS `u`
JOIN (
  SELECT `id`
  FROM `contacts`
  WHERE `id` NOT IN (?, ?, ?)
) AS `c`
  ON `u`.`id` = `c`.`id`
```
{% endcode %}

Alternatively, a function may be used to define the derived table:

{% code title="QueryBuilder" %}
```javascript
query.from( "users as u" )
    .joinSub( "c", function ( q ) {
        q.select( "id" )
            .from( "contacts" )
            .whereNotIn( "id", [ 1, 2, 3 ] );
    }, "u.id", "=", "c.id" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users` AS `u`
JOIN (
  SELECT `id`
  FROM `contacts`
  WHERE `id` NOT IN (?, ?, ?)
) AS `c`
  ON `u`.`id` = `c`.`id`
```
{% endcode %}

Complex join conditions are also possible by passing a function as the third parameter:

{% code title="QueryBuilder" %}
```javascript
query.from( "users as u" )
    .joinSub( "c", function ( q ) {
        q.select( "id" )
            .from( "contacts" )
            .whereNotIn( "id", [ 1, 2, 3 ] );
    }, function( j ) {
        j.on( "u.id", "c.id" );
        j.on( "u.type", "c.type" );
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users` AS `u`
JOIN (
  SELECT `id`
  FROM `contacts`
  WHERE `id` NOT IN (?, ?, ?)
) AS `c`
  ON `u`.`id` = `c`.`id`
  AND `u`.`type` = `c`.`type`
```
{% endcode %}

## leftJoin <a id="leftjoin"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |


<table>
  <thead>
    <tr>
      <th style="text-align:left">table</th>
      <th style="text-align:left">string | <a href="raw-expressions.md">Expression</a> | <a href="joins.md#joinclause">JoinClause</a>
      </th>
      <th style="text-align:left"><code>true</code>
      </th>
      <th style="text-align:left">&#x200B;</th>
      <th style="text-align:left">
        <p>The name of the table or a <a href="raw-expressions.md"><code>Expression</code></a> object
          from which the query is based. Alternatively, a configured <a href="joins.md#joinclause"><code>JoinClause</code></a> instance
          can be passed.</p>
        <p>(Note: a <a href="joins.md#joinclause"><code>JoinClause</code></a> instance
          may have a different join type than a <code>left</code> join. The <a href="joins.md#joinclause"><code>JoinClause</code></a> instance&apos;s
          join type will be used.)</p>
      </th>
    </tr>
  </thead>
  <tbody></tbody>
</table>

| first | string \| Expression \| Function | `false` |  | The first column or [`Expression`](raw-expressions.md) to join the table on. Alternatively, a function can be passed to configure complex join statements. |
| :--- | :--- | :--- | :--- | :--- |


| operator | string | `false` | `"="` | The boolean operator for the join clause. |
| :--- | :--- | :--- | :--- | :--- |


| second | string \| [Expression](raw-expressions.md) | `false` |  | The second column or [`Expression`](raw-expressions.md) to join the table on. |
| :--- | :--- | :--- | :--- | :--- |


| where | boolean | `false` | `false` | Sets if the value of `second` should be interpreted as a column or a value. Passing this as an argument is discouraged. Use a closure to define the where clauses where possible. |
| :--- | :--- | :--- | :--- | :--- |


{% code title="QueryBuilder" %}
```javascript
query.from( "posts" )
    .leftJoin( "users", "users.id", "posts.author_id" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `posts`
LEFT JOIN `users`
  ON `users`.`id` = `posts`.`author_id`
```
{% endcode %}

## leftJoinRaw <a id="leftjoinraw"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| table | string | `true` | ​ | The raw SQL string to use as the table. |
| first | string \| [Expression](raw-expressions.md) \| Function | `false` |  | The first column or [`Expression`](raw-expressions.md) to join the table on.  Alternatively, a function can be passed to configure complex join statements. |
| operator | string | `false` | `"="` | The boolean operator for the join clause. |
| second | string \| [Expression](raw-expressions.md) | `false` |  | The second column or [`Expression`](raw-expressions.md) to join the table on. |
| where | boolean | `false` | `false` | Sets if the value of `second` should be interpreted as a column or a value. Passing this as an argument is discouraged. Use a closure to define the where clauses where possible. |

Uses the raw SQL provided to as the table for the left join clause. All the other functionality of `leftJoinRaw` matches the [`join`](joins.md#join) method.

{% code title="QueryBuilder" %}
```javascript
query.from( "posts" )
    .leftJoinRaw( "users (nolock)", "users.id", "posts.author_id" );
```
{% endcode %}

{% code title="SQL Server" %}
```sql
SELECT *
FROM [posts]
LEFT JOIN users (nolock)
  ON [users].[id] = [posts].[author_id]
```
{% endcode %}

{% hint style="warning" %}
Using `leftJoinRaw` will most likely tie your code to a specific database, so think carefully before using the `leftJoinRaw` method if you want your project to be database agnostic.
{% endhint %}

## leftJoinSub <a id="leftjoinsub"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| alias | string | `true` |  | The alias for the derived table. |
| input | Function \| QueryBuilder | `true` | ​ | Either a `QueryBuilder` instance or a function to define the derived query. |
| first | string \| [Expression](raw-expressions.md) \| Function | `true` |  | The first column or [`Expression`](raw-expressions.md) to join the table on.  Alternatively, a function can be passed to configure complex join statements. |
| operator | string | `false` | `"="` | The boolean operator for the join clause. |
| second | string \| [Expression](raw-expressions.md) | `false` |  | The second column or [`Expression`](raw-expressions.md) to join the table on. |
| where | boolean | `false` | `false` | Sets if the value of `second` should be interpreted as a column or a value. Passing this as an argument is discouraged. Use a closure to define the where clauses where possible. |

Adds a left join to a derived table. All the functionality of the [`joinSub`](joins.md#joinsub) method applies to define and constrain the query.

{% code title="QueryBuilder" %}
```javascript
var sub = query.newQuery()
    .select( "id" )
    .from( "contacts" )
    .whereNotIn( "id", [ 1, 2, 3 ] );

query.from( "users as u" )
    .leftJoinSub( "c", sub, "u.id", "=", "c.id" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users` AS `u`
LEFT JOIN (
  SELECT `id`
  FROM `contacts`
  WHERE `id` NOT IN (?, ?, ?)
) AS `c`
  ON `u`.`id` = `c`.`id`
```
{% endcode %}

## rightJoin <a id="get"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |


<table>
  <thead>
    <tr>
      <th style="text-align:left">table</th>
      <th style="text-align:left">string | <a href="raw-expressions.md">Expression</a> | <a href="joins.md#joinclause">JoinClause</a>
      </th>
      <th style="text-align:left"><code>true</code>
      </th>
      <th style="text-align:left">&#x200B;</th>
      <th style="text-align:left">
        <p>The name of the table or a <a href="raw-expressions.md"><code>Expression</code></a> object
          from which the query is based. Alternatively, a configured <a href="joins.md#joinclause"><code>JoinClause</code></a> instance
          can be passed.</p>
        <p>(Note: a <a href="joins.md#joinclause"><code>JoinClause</code></a> instance
          may have a different join type than a <code>right</code> join. The <a href="joins.md#joinclause"><code>JoinClause</code></a> instance&apos;s
          join type will be used.)</p>
      </th>
    </tr>
  </thead>
  <tbody></tbody>
</table>

| first | string \| [Expression](raw-expressions.md) \| Function | `false` |  | The first column or [`Expression`](raw-expressions.md) to join the table on. Alternatively, a function can be passed to configure complex join statements. |
| :--- | :--- | :--- | :--- | :--- |


| operator | string | `false` | `"="` | The boolean operator for the join clause. |
| :--- | :--- | :--- | :--- | :--- |


| second | string \| [Expression](raw-expressions.md) | `false` |  | The second column or [`Expression`](raw-expressions.md) to join the table on. |
| :--- | :--- | :--- | :--- | :--- |


| where | boolean | `false` | `false` | Sets if the value of `second` should be interpreted as a column or a value. Passing this as an argument is discouraged. Use a closure to define the where clauses where possible. |
| :--- | :--- | :--- | :--- | :--- |


{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .rightJoin( "posts", "users.id", "posts.author_id" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
RIGHT JOIN `posts`
  ON `users`.`id` = `posts`.`author_id`
```
{% endcode %}

## rightJoinRaw <a id="rightjoinraw"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| table | string | `true` | ​ | The raw SQL string to use as the table. |
| first | string \| [Expression](raw-expressions.md) \| Function | `false` |  | The first column or [`Expression`](raw-expressions.md) to join the table on.  Alternatively, a function can be passed to configure complex join statements. |
| operator | string | `false` | `"="` | The boolean operator for the join clause. |
| second | string \| [Expression](raw-expressions.md) | `false` |  | The second column or [`Expression`](raw-expressions.md) to join the table on. |
| where | boolean | `false` | `false` | Sets if the value of `second` should be interpreted as a column or a value. Passing this as an argument is discouraged. Use a closure to define the where clauses where possible. |

Uses the raw SQL provided to as the table for the right join clause. All the other functionality of `rightJoinRaw` matches the [`join`](joins.md#join) method.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .rightJoinRaw( "posts (nolock)", "users.id", "posts.author_id" );
```
{% endcode %}

{% code title="SQL Server" %}
```sql
SELECT *
FROM [users]
LEFT JOIN posts (nolock)
  ON [users].[id] = [posts].[author_id]
```
{% endcode %}

{% hint style="warning" %}
Using `rightJoinRaw` will most likely tie your code to a specific database, so think carefully before using the `rightJoinRaw` method if you want your project to be database agnostic.
{% endhint %}

## rightJoinSub <a id="rightjoinsub"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| alias | string | `true` |  | The alias for the derived table. |
| input | Function \| QueryBuilder | `true` | ​ | Either a `QueryBuilder` instance or a function to define the derived query. |
| first | string \| [Expression](raw-expressions.md) \| Function | `true` |  | The first column or [`Expression`](raw-expressions.md) to join the table on.  Alternatively, a function can be passed to configure complex join statements. |
| operator | string | `false` | `"="` | The boolean operator for the join clause. |
| second | string \| [Expression](raw-expressions.md) | `false` |  | The second column or [`Expression`](raw-expressions.md) to join the table on. |
| where | boolean | `false` | `false` | Sets if the value of `second` should be interpreted as a column or a value. Passing this as an argument is discouraged. Use a closure to define the where clauses where possible. |

Adds a right join to a derived table. All the functionality of the [`joinSub`](joins.md#joinsub) method applies to define and constrain the query.

{% code title="QueryBuilder" %}
```javascript
var sub = query.newQuery()
    .select( "id" )
    .from( "contacts" )
    .whereNotIn( "id", [ 1, 2, 3 ] );

query.from( "users as u" )
    .rightJoinSub( "c", sub, "u.id", "=", "c.id" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users` AS `u`
RIGHT JOIN (
  SELECT `id`
  FROM `contacts`
  WHERE `id` NOT IN (?, ?, ?)
) AS `c`
  ON `u`.`id` = `c`.`id`
```
{% endcode %}

## crossJoin <a id="crossjoin"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |


<table>
  <thead>
    <tr>
      <th style="text-align:left">table</th>
      <th style="text-align:left">string | <a href="raw-expressions.md">Expression</a> | <a href="joins.md#joinclause">JoinClause</a>
      </th>
      <th style="text-align:left"><code>true</code>
      </th>
      <th style="text-align:left">&#x200B;</th>
      <th style="text-align:left">
        <p>The name of the table or a <a href="raw-expressions.md"><code>Expression</code></a> object
          from which the query is based. Alternatively, a configured <a href="joins.md#joinclause"><code>JoinClause</code></a> instance
          can be passed.</p>
        <p>(Note: a <a href="joins.md#joinclause"><code>JoinClause</code></a> instance
          may have a different join type than a <code>cross</code> join. The <a href="joins.md#joinclause"><code>JoinClause</code></a> instance&apos;s
          join type will be used.)</p>
      </th>
    </tr>
  </thead>
  <tbody></tbody>
</table>

{% code title="QueryBuilder" %}
```javascript
query.from( "users" ).crossJoin( "posts" );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
CROSS JOIN `posts`
```
{% endcode %}

## crossJoinRaw <a id="crossjoinraw"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| table | string | `true` | ​ | The raw SQL string to use as the table. |

Uses the raw SQL provided to as the table for the cross join clause. Cross joins cannot be further constrained with `on` or `where` clauses.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" ).crossJoinRaw( "posts (nolock)" );
```
{% endcode %}

{% code title="SQL Server" %}
```sql
SELECT *
FROM [users]
CROSS JOIN posts (nolock)
```
{% endcode %}

{% hint style="warning" %}
Using `crossJoinRaw` will most likely tie your code to a specific database, so think carefully before using the `crossJoinRaw` method if you want your project to be database agnostic.
{% endhint %}

## crossJoinSub <a id="crossjoinsub"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| alias | string | `true` |  | The alias for the derived table. |
| input | Function \| QueryBuilder | `true` | ​ | Either a `QueryBuilder` instance or a function to define the derived query. |

Adds a cross join to a derived table. The derived table can be defined using a `QueryBuilder` instance or a function just as with [`joinSub`](joins.md#joinsub). Cross joins cannot be constrained, however.

{% code title="QueryBuilder" %}
```javascript
var sub = query.newQuery()
    .select( "id" )
    .from( "contacts" )
    .whereNotIn( "id", [ 1, 2, 3 ] );

query.from( "users as u" ).crossJoinSub( "c", sub );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users` AS `u`
CROSS JOIN (
  SELECT `id`
  FROM `contacts`
  WHERE `id` NOT IN (?, ?, ?)
)
```
{% endcode %}

## newJoin <a id="newjoin"></a>

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| table | string \| [Expression](raw-expressions.md) | `true` | ​ | The name of the table or a [`Expression`](raw-expressions.md) object from which the query is based. |
| type | string | `false` | `"inner"` | The type of the join.  Valid types are `inner`, `left`, `right`, or `cross`. |

Creates a new [`JoinClause`](joins.md#joinclause). A [`JoinClause`](joins.md#joinclause) is a specialized version of a `QueryBuilder`. You may call `on` or `orOn` to constrain the [`JoinClause`](joins.md#joinclause). You may also call any [`where`](wheres.md) methods.

Creating a [`JoinClause`](joins.md#joinclause) directly is useful when you need to share a join between different queries. You can create and configure the [`JoinClause`](joins.md#joinclause) in a function and pass it to queries as needed.

{% code title="QueryBuilder" %}
```javascript
var j = query.newJoin( "contacts" )
    .on( "users.id", "posts.author_id" );

query.from( "users" ).join( j );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
JOIN `posts`
  ON `users`.`id` = `posts`.`author_id`
```
{% endcode %}

{% hint style="warning" %}
Although a [`JoinClause`](joins.md#joinclause) can be passed to [`join`](joins.md#join), [`leftJoin`](joins.md#leftjoin), [`rightJoin`](joins.md#get), and `crossJoin`, the type of the [`JoinClause`](joins.md#joinclause) will override the type of the function.
{% endhint %}

{% code title="QueryBuilder" %}
```javascript
// This is still an inner join because
// the JoinClause is an inner join
var j = query.newJoin( "contacts", "inner" )
    .on( "users.id", "posts.author_id" );

query.from( "users" ).leftJoin( j );
```
{% endcode %}

{% code title="MySQL" %}
```sql
-- This is still an inner join because
-- the JoinClause is an inner join
SELECT *
FROM `users`
JOIN `posts`
  ON `users`.`id` = `posts`.`author_id`
```
{% endcode %}

## JoinClause

A `JoinClause` is a specialized version of a `QueryBuilder`. You may call `on` or `orOn` to constrain the `JoinClause`. You may also call any [`where`](wheres.md) methods.

### on

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| first | string \| [Expression](raw-expressions.md) \| Function | `false` |  | The first column or [`Expression`](raw-expressions.md)of the condition.  Alternatively, a function can be passed to nest conditions with parenthesis. |
| operator | string | `false` | `"="` | The boolean operator for the condition. |
| second | string \| [Expression](raw-expressions.md) | `false` |  | The second column or [`Expression`](raw-expressions.md) of the condition. |
| combinator | string | `false` | `"and"` | The boolean combinator for the clause \(e.g. "and" or "or"\). |

Applies a join condition to the `JoinClause`. An alias for `whereColumn`.

{% code title="QueryBuilder" %}
```javascript
var j = query.newJoin( "contacts" )
    .on( "users.id", "posts.author_id" );

query.from( "users" ).join( j );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
JOIN `posts`
  ON `users`.`id` = `posts`.`author_id`
```
{% endcode %}

### orOn

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| first | string \| [Expression](raw-expressions.md) \| Function | `false` |  | The first column or [`Expression`](raw-expressions.md)of the condition.  Alternatively, a function can be passed to nest conditions with parenthesis. |
| operator | string | `false` | `"="` | The boolean operator for the condition. |
| second | string \| [Expression](raw-expressions.md) | `false` |  | The second column or [`Expression`](raw-expressions.md) of the condition. |

Applies a join condition to the `JoinClause` using an `or` combinator. An alias for `orWhereColumn`.

{% code title="QueryBuilder" %}
```javascript
var j = query.newJoin( "contacts" )
    .on( "users.id", "posts.author_id" )
    .orOn( "users.id", "posts.reviewer_id" );

query.from( "users" ).join( j );
```
{% endcode %}

{% code title="MySQL" %}
```sql
SELECT *
FROM `users`
JOIN `posts`
  ON `users`.`id` = `posts`.`author_id`
  OR `users`.`id` = `posts`.`reviewer_id`
```
{% endcode %}

## Preventing Duplicate Joins

You can optionally configure qb to ignore duplicate joins.  With this setting turned on each `JoinClause` is inspected and checked if it matches any existing `JoinClause` instances on the query.  This is useful if you have a table shared between optional constraints and want to ensure it is only added once.

You can opt-in to this behavior by setting `preventDuplicateJoins = true` in your `moduleSettings` in `config/ColdBox.cfc`.

```javascript
moduleSettings = {
    "qb": {
         "preventDuplicateJoins": true  
    }
};
```

