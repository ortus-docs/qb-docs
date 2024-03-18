# Inserts, Updates, and Deletes

The following methods all have the same return value:

```javascript
{
    "result": "Value of the `result` parameter to `queryExecute`",
    "query": "Return value of running `queryExecute` - a CFML query object"
}
```

{% hint style="info" %}
`insert`, `update`, and `delete` actions always return a query object for `query`, regardless of your configured `returnFormat`.
{% endhint %}

## insert

| Name    | Type                     | Required | Default | Description                                                                               |
| ------- | ------------------------ | -------- | ------- | ----------------------------------------------------------------------------------------- |
| values  | struct \| array\<struct> | `true`   |         | A struct or array of structs to insert in to the table.                                   |
| options | struct                   | `false`  | `{}`    | Any additional `queryExecute` options.                                                    |
| toSQL   | boolean                  | `false`  | `false` | If `true`, returns the raw SQL string instead of running the query. Useful for debugging. |

{% hint style="warning" %}
This call must come after setting the query's table using [`from`](../building-queries/from.md#get) or [`table`](../building-queries/from.md#get-1).
{% endhint %}

You can insert a single record by passing a struct:

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .insert( {
        "name" = "Robert",
        "email" = "robert@test.com",
        "age" = 55
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
INSERT INTO `users` (`age`, `email`, `name`)
VALUES (?, ?, ?)
```
{% endcode %}

You can specify any [query param](../building-queries/parameters-and-bindings.md#custom-parameter-types) options such as the SQL type by passing a struct with the parameters you would pass to [`cfqueryparam`](https://cfdocs.org/cfqueryparam).

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .insert( {
        "name" = "Robert",
        "email" = "robert@test.com",
        "age" = { value = 55, cfsqltype = "CF_SQL_INTEGER" }
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
INSERT INTO `users` (`age`, `email`, `name`)
VALUES (?, ?, ?)
```
{% endcode %}

Raw values can be supplied to an insert statement.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .insert( {
        "name" = "Robert",
        "email" = "robert@test.com",
        "updatedDate" = query.raw( "NOW()" )
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
INSERT INTO `users` (`age`, `email`, `updatedDate`)
VALUES (?, ?, NOW())
```
{% endcode %}

Multiple rows can be inserted in a batch by passing an array of structs to `insert`.

{% hint style="info" %}
This is not the same as looping over and array and calling `insert` in the loop.  Using an array with `insert` will batch the inserts in one SQL call.  Looping over an array and calling `insert` each time will create a SQL request for each item in the array.  Bottom line, pass your array to `insert`!
{% endhint %}

{% code title="QueryBuilder" %}
```javascript
query.from( "users" ).insert( [
    { "email" = "john@example.com", "name" = "John Doe" },
    { "email" = "jane@example.com", "name" = "Jane Doe" }
] );
```
{% endcode %}

{% tabs %}
{% tab title="MySQL" %}
```sql
INSERT INTO `users` (`email`, `name`)
VALUES (?, ?), (?, ?)
```
{% endtab %}

{% tab title="Oracle" %}
```sql
INSERT ALL
INTO "USERS" ("EMAIL", "NAME") VALUES (?, ?)
INTO "USERS" ("EMAIL", "NAME") VALUES (?, ?)
SELECT 1 FROM dual
```
{% endtab %}
{% endtabs %}

## insertIgnore

| Name    | Type                     | Required | Default | Description                                                                               |
| ------- | ------------------------ | -------- | ------- | ----------------------------------------------------------------------------------------- |
| values  | struct \| array\<struct> | true     |         | A struct or array of structs to insert in to the table.                                   |
| target  | array\<string>           | false    | `[]`    | An array of key column names to match on. (SQL Server and Oracle grammars only.)          |
| options | struct                   | false    | `{}`    | Any additional `queryExecute` options.                                                    |
| toSQL   | boolean                  | false    | `false` | If `true`, returns the raw SQL string instead of running the query. Useful for debugging. |

{% hint style="warning" %}
This call must come after setting the query's table using [`from`](../building-queries/from.md#get) or [`table`](../building-queries/from.md#get-1).
{% endhint %}

Inserts data into a table while ignoring duplicate key conflicts.

{% hint style="info" %}
`target` is only required for `SQLServerGrammar` and `OracleGrammar`
{% endhint %}

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .insertIgnore(
        values = [
            { "email" = "foo", "name" = "bar" },
            { "email" = "baz", "name" = "bam" }
        ],
        target = [ "email" ]
    );
```
{% endcode %}

{% tabs %}
{% tab title="MySQL" %}
```sql
INSERT IGNORE INTO `users` (`email`, `name`)
VALUES (?, ?), (?, ?)
```
{% endtab %}

{% tab title="SQL Server" %}
```sql
MERGE [users] AS [qb_target]
USING (VALUES (?, ?), (?, ?)) AS [qb_src] ([email], [name])
ON [qb_target].[email] = [qb_src].[email]
WHEN NOT MATCHED BY TARGET THEN
INSERT ([email], [name]) VALUES ([email], [name]);
```
{% endtab %}

{% tab title="Postgres" %}
```sql
INSERT INTO "users" ("email", "name")
VALUES (?, ?), (?, ?)
ON CONFLICT DO NOTHING
```
{% endtab %}

{% tab title="Oracle" %}
```sql
MERGE INTO "USERS" "QB_TARGET"
USING (SELECT ?, ? FROM dual UNION ALL SELECT ?, ? FROM dual) "QB_SRC"
ON "QB_TARGET"."EMAIL" = "QB_SRC"."EMAIL"
WHEN NOT MATCHED THEN
INSERT ("EMAIL", "NAME")
VALUES ("QB_SRC"."EMAIL", "QB_SRC"."NAME")
```
{% endtab %}
{% endtabs %}

## insertUsing

| Name    | Type                     | Required | Default | Description                                                                                                                                |
| ------- | ------------------------ | -------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| source  | function \| QueryBuilder | true     |         | A callback or builder instance to serve as the source of the insert.                                                                       |
| columns | array\<string>           | false    |         | An array of column names that will be inserted. If no columns are passed, the columns will be derived from the source columns and aliases. |
| options | struct                   | false    | `{}`    | Any additional `queryExecute` options.                                                                                                     |
| toSQL   | boolean                  | false    | `false` | If `true`, returns the raw SQL string instead of running the query. Useful for debugging.                                                  |

{% hint style="warning" %}
This call must come after setting the query's table using [`from`](../building-queries/from.md#get) or [`table`](../building-queries/from.md#get-1).
{% endhint %}

Inserts data into a table using a subquery as the source.

```javascript
qb.from( "users" )
    .insertUsing( function( q ) {
        q.from( "activeDirectoryUsers" )
            .select( [ "email", "modifiedDate AS createdDate" ] )
            .where( "active", 1 );
    } );
```

```sql
INSERT INTO `users` (`email`, `createdDate`)
SELECT `email`, `modifiedDate` AS `createdDate`
FROM `activeDirectoryUsers`
WHERE `active` = ?
```

You can also pass in an array of column names to avoid aliasing in your source query.

```javascript
qb.from( "users" )
    .insertUsing(
        columns = [ "email", "createdDate" ],
        source = function( q ) {
            q.from( "activeDirectoryUsers" )
                 .select( [ "email", "modifiedDate" ] )
                 .where( "active", 1 );
        }
    );
```

```sql
INSERT INTO `users` (`email`, `createdDate`)
SELECT `email`, `modifiedDate`
FROM `activeDirectoryUsers`
WHERE `active` = ?
```

Alternatively, the source can be defined as a QueryBuilder object:

```cfscript
qb.from( "users" )
    .insertUsing(
        qb.newQuery()
            .from( "activeDirectoryUsers" )
            .select( [ "email", "modifiedDate AS createdDate" ] )
            .where( "active", 1 )
    );
```

```sql
INSERT INTO `users` (`email`, `createdDate`)
SELECT `email`, `modifiedDate` AS `createdDate`
FROM `activeDirectoryUsers`
WHERE `active` = ?
```

## update

| Name    | Type    | Required | Default | Description                                                                                                                                                                      |
| ------- | ------- | -------- | ------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| values  | struct  | `false`  | `{}`    | A struct of column and value pairs to update.  These column and value pairs are appended to any already set with the [`addUpdate`](inserts-updates-deletes.md#addupdate) method. |
| options | struct  | `false`  | `{}`    | Any additional `queryExecute` options.                                                                                                                                           |
| toSQL   | boolean | `false`  | `false` | If `true`, returns the raw SQL string instead of running the query. Useful for debugging.                                                                                        |

{% hint style="warning" %}
This call must come after setting the query's table using [`from`](../building-queries/from.md#get) or [`table`](../building-queries/from.md#get-1).
{% endhint %}

Updates a table with a struct of column and value pairs.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .update( {
        "email" = "foo",
        "name" = "bar"
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
UPDATE `users`
SET `email` = ?,
    `name` = ?
```
{% endcode %}

You can specify any [query param](../building-queries/parameters-and-bindings.md#custom-parameter-types) options such as the SQL type by passing a struct with the parameters you would pass to [`cfqueryparam`](https://cfdocs.org/cfqueryparam).

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .update( {
        "email" = "foo",
        "name" = "bar",
        "updatedDate" = { value = now(), cfsqltype = "CF_SQL_TIMESTAMP" }
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
UPDATE `users`
SET `email` = ?,
    `name` = ?,
    `updatedDate` = ?
```
{% endcode %}

Any constraining of the update query should be done using the appropriate [WHERE](../building-queries/wheres.md) statement before calling `update`.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereId( 1 )
    .update( {
        "email" = "foo",
        "name" = "bar"
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
UPDATE `users`
SET `email` = ?,
    `name` = ?
WHERE `Id` = ?
```
{% endcode %}

You can update a column based on another column using a raw expression.

{% code title="QueryBuilder" %}
```javascript
query.from( "hits" )
    .where( "page", "someUrl" )
    .update( {
        "count" = query.raw( "count + 1" )
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
UPDATE `hits`
SET `count` = count + 1
WHERE `page` = ?
```
{% endcode %}

### Updating Null values

Null values can be inserted by using queryparam syntax:

```sql
query.from("user")
		.whereId( 10 )
		.update( {
			manager_FK = { value = "", null=true },
		} )
```

if you are using full null support the following (easier) syntax is also allowed:

```sql
query.from("user")
		.whereId( 10 )
		.update( {
			manager_FK = { value = null },
		} )
```

### Updating with Subselects

Subselects can be used to update values by passing a closure as the value

```sql
qb.table( "employees" )
    .update( {
		    "departmentName" = function( q ) {
		        q.from( "departments" )
		            .select( "name" )
		            .whereColumn( "employees.departmentId", "departments.id" );
		    } )
		} );
```

{% tabs %}
{% tab title="MySQL" %}
```sql
UPDATE `employees`
SET `departmentName` = (
    SELECT `name`
    FROM `departments`
    WHERE `employees`.`departmentId` = `departments`.`id`
)
```
{% endtab %}
{% endtabs %}

You can also pass a builder instance in place of the closure.

```sql
qb.table( "employees" )
    .update( {
		    "departmentName" = qb.newQuery()
		        .from( "departments" )
		        .select( "name" )
		        .whereColumn( "employees.departmentId", "departments.id" )
		    } )
		} );
```

### Updating with Joins

qb will correctly format `JOIN` clauses in your `UPDATE` statements for your database grammar.

{% hint style="danger" %}
`OracleGrammar` **does not support** `JOIN` clauses in`UPDATE` statements.  Consider using [subselects](inserts-updates-deletes.md#updating-with-subselects) in your `UPDATE` statement instead.
{% endhint %}

```sql
qb.table( "employees" )
    .join( "departments", "departments.id", "employees.departmentId" )
    .update( {
        "employees.departmentName": qb.raw( "departments.name" )
    } );
```

{% tabs %}
{% tab title="MySQL" %}
```sql
UPDATE `employees`
INNER JOIN `departments`
    ON `departments`.`id` = `employees`.`departmentId`
SET `employees`.`departmentName` = departments.name
```
{% endtab %}

{% tab title="SQL Server" %}
```sql
UPDATE [employees]
SET [employees].[departmentName] = departments.name
FROM [employees]
INNER JOIN [departments]
    ON [departments].[id] = [employees].[departmentId]
```
{% endtab %}

{% tab title="Postgres" %}
```sql
UPDATE "employees"
SET "employees"."departmentName" = departments.name
FROM "departments"
WHERE "departments"."id" = "employees"."departmentId"
```
{% endtab %}
{% endtabs %}

## addUpdate

| Name   | Type   | Required | Default | Description                                                     |
| ------ | ------ | -------- | ------- | --------------------------------------------------------------- |
| values | struct | `true`   |         | A struct of column and value pairs to add to the update clause. |

Adds values to a later [`update`](inserts-updates-deletes.md#update), similar to [`addSelect`](../building-queries/selects.md#get-2).

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .whereId( 1 )
    .addUpdate( {
        "email" = "foo",
        "name" = "bar"
    } )
    .when( true, function( q ) {
        q.addUpdate( {
            "foo": "yes"
        } );
    } )
    .when( false, function( q ) {
        q.addUpdate( {
            "bar": "no"
        } );
    } )
    .update();
```
{% endcode %}

{% code title="MySQL" %}
```sql
UPDATE `users`
SET `email` = ?,
    `foo` = ?,
    `name` = ?
WHERE `Id` = ?
```
{% endcode %}

## updateOrInsert

| Name    | Type    | Required | Default | Description                                                                               |
| ------- | ------- | -------- | ------- | ----------------------------------------------------------------------------------------- |
| values  | struct  | `true`   |         | A struct of column and value pairs to either update or insert.                            |
| options | boolean | `false`  | `{}`    | Any additional `queryExecute` options.                                                    |
| toSql   | boolean | `false`  | `false` | If `true`, returns the raw SQL string instead of running the query. Useful for debugging. |

Performs an update statement if the configured query returns `true` for `exists`.  Otherwise, performs an insert statement.

If an update statement is performed qb applies a `limit( 1 )` to the update statement.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "email", "foo" )
    .updateOrInsert( {
        "email" = "foo",
        "name" = "baz"
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
UPDATE `users`
SET `email` = ?,
    `name` = ?
WHERE `email` = ?
LIMIT 1
```
{% endcode %}

If the configured query returns 0 records, then an insert statement is performed.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "email", "foo" )
    .updateOrInsert( {
        "email" = "foo",
        "name" = "baz"
    } );
```
{% endcode %}

{% code title="MySQL" %}
```sql
INSERT INTO `users` (`email`, `name`)
VALUES (?, ?)
```
{% endcode %}

## upsert

| Name           | Type                                       | Required | Default | Description                                                                                                                                                                                                                                                        |
| -------------- | ------------------------------------------ | -------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| values         | struct \| array\<struct> \| array\<string> | `true`   |         | A struct or array of structs to insert into or update on the table. If a `source` is provided, this should be an array of column names to update instead.                                                                                                          |
| target         | string \| array\<string>                   | `true`   |         | A column name or array of column names to match the values to the table. If a match is found, the record will be updated. Otherwise, a new record will be inserted.  Most database grammars required these columns to have either a primary key or a unique index. |
| update         | array \| struct                            | `false`  | `null`  | Either an array of columns to update using the current `value` matched or a struct containing the column names as keys and the corresponding to update.  If blank, it will update all the columns in the passed in `value`.                                        |
| source         | function \| QueryBuilder                   | `false`  | `null`  | A callback function or QueryBuilder object to use as the source for the upsert. When using this parameter, `values` must be an array of column names to update.                                                                                                    |
| deleteUmatched | boolean                                    | `false`  | `false` | Boolean flag to delete any unmatched source records as part the upsert. (SQL Server only.)                                                                                                                                                                         |
| options        | boolean                                    | `false`  | `{}`    | Any additional `queryExecute` options.                                                                                                                                                                                                                             |
| toSql          | boolean                                    | `false`  | `false` | If `true`, returns the raw SQL string instead of running the query. Useful for debugging.                                                                                                                                                                          |

An upsert is a batch operation that either inserts or updates a row depending on if a target match is found.  If a row is matched with the target column(s), then the matched row is updated.  Otherwise a new row is inserted.&#x20;

{% hint style="warning" %}
In most database grammars, the target columns are required to be primary key or unique indexes.
{% endhint %}

```sql
qb.table( "users" )
    .upsert(
        values = [
            {
                "username": "johndoe",
                "active": 1,
                "createdDate": "2021-09-08 12:00:00",
                "modifiedDate": "2021-09-08 12:00:00"
            },
            {
                "username": "janedoe",
                "active": 1,
                "createdDate": "2021-09-10 10:42:13",
                "modifiedDate": "2021-09-10 10:42:13"
            },
        ],
        target = [ "username" ],
        update = [ "active", "modifiedDate" ],
    );
```

{% tabs %}
{% tab title="MySQL" %}
```sql
INSERT INTO `users`
    (`active`, `createdDate`, `modifiedDate`, `username`)
VALUES
    (?, ?, ?, ?),
    (?, ?, ?, ?)
ON DUPLICATE KEY UPDATE
    `active` = VALUES(`active`),
    `modifiedDate` = VALUES(`modifiedDate`)
```
{% endtab %}

{% tab title="SQL Server" %}
```sql
MERGE [users] AS [qb_target]
USING (VALUES (?, ?, ?, ?), (?, ?, ?, ?)) AS [qb_src]
    ([active], [createdDate], [modifiedDate], [username])
ON [qb_target].[username] = [qb_src].[username]
WHEN MATCHED THEN UPDATE
    SET [active] = [qb_src].[active],
        [modifiedDate] = [qb_src].[modifiedDate]
WHEN NOT MATCHED BY TARGET THEN INSERT
    ([active], [createdDate], [modifiedDate], [username])
    VALUES
    ([active], [createdDate], [modifiedDate], [username])
```
{% endtab %}

{% tab title="Postgres" %}
```sql
INSERT INTO "users"
    ("active", "createdDate", "modifiedDate", "username")
VALUES
    (?, ?, ?, ?),
    (? ,? ,? ,?)
ON CONFLICT ("username") DO UPDATE
    "active" = EXCLUDED."active",
    "modifiedDate" = EXCLUDED."modifiedDate"
```
{% endtab %}

{% tab title="Oracle" %}
```sql
MERGE INTO "USERS" "QB_TARGET"
USING (
    SELECT ?, ?, ?, ? FROM dual
    UNION ALL
    SELECT ?, ?, ?, ? FROM dual
) "QB_SRC"
ON "QB_TARGET"."USERNAME" = "QB_SRC"."USERNAME"
WHEN MATCHED THEN UPDATE
    SET "ACTIVE" = "QB_SRC"."ACTIVE",
        "MODIFIEDDATE" = "QB_SRC"."MODIFIEDDATE"
WHEN NOT MATCHED THEN INSERT
    ("ACTIVE", "CREATEDDATE", "MODIFIEDDATE", "USERNAME")
    VALUES
    ("QB_SRC"."ACTIVE", "QB_SRC"."CREATEDDATE", "QB_SRC"."MODIFIEDDATE", "QB_SRC"."USERNAME")
```
{% endtab %}
{% endtabs %}

The update clause in a upsert can also accept raw values, making it very useful for tracking data like statistics.

```sql
qb.table( "stats" )
    .upsert(
        values = [
            { "postId": 1, "viewedDate": "2021-09-08", "views": 1 },
            { "postId": 2, "viewedDate": "2021-09-08", "views": 1 }
        ],
        target = [ "postId", "viewedDate" ],
        update = { "views": qb.raw( "stats.views + 1" ) }
    );
```

{% tabs %}
{% tab title="MySQL" %}
```sql
INSERT INTO `stats`
    (`postId`, `viewedDate`, `views`)
VALUES
    (?, ?, ?),
    (?, ?, ?)
ON DUPLICATE KEY UPDATE
    `views` = stats.views + 1
```
{% endtab %}

{% tab title="SQL Server" %}
```sql
MERGE [stats] AS [qb_target]
USING (VALUES (?, ?, ?), (?, ?, ?)) AS [qb_src]
    ([postId], [viewedDate], [views])
ON [qb_target].[postId] = [qb_src].[postId]
    AND [qb_target].[viewedDate] = [qb_src].[viewedDate]
WHEN MATCHED THEN UPDATE
    SET [views] = stats.views + 1
WHEN NOT MATCHED BY TARGET THEN INSERT
    ([postId], [viewedDate], [views])
    VALUES
    ([postId], [viewedDate], [views])
```
{% endtab %}

{% tab title="Postgres" %}
```sql
INSERT INTO "stats"
    ("postId", "viewedDate", "views")
VALUES
    (?, ?, ?),
    (?, ?, ?)
ON CONFLICT ("postId", "viewedDate") DO UPDATE
    "views" = stats.views + 1
```
{% endtab %}

{% tab title="Oracle" %}
```sql
MERGE INTO "STATS" "QB_TARGET"
USING (
    SELECT ?, ?, ? FROM dual
    UNION ALL
    SELECT ?, ?, ? FROM dual
) "QB_SRC"
ON "QB_TARGET"."POSTID" = "QB_SRC"."POSTID"
    AND "QB_TARGET"."VIEWEDDATE" = "QB_SRC"."VIEWEDDATE"
WHEN MATCHED THEN UPDATE
    SET "VIEWS" = stats.views + 1
WHEN NOT MATCHED THEN INSERT
    ("POSTID", "VIEWEDDATE", "VIEWS")
    VALUES
    ("QB_SRC"."POSTID", "QB_SRC"."VIEWEDDATE", "QB_SRC"."VIEWS")
```
{% endtab %}
{% endtabs %}

A source callback or QueryBuilder instance can be used instead of explicit values.  This allows you to do upserts across tables or subqueries.

To do this, provide a `source` that is either a function to configure a new QueryBuilder instance or an already configured QueryBuilder instance.  Then specify the columns that will be affected as an array of strings to `values`.

```sql
qb.table( "stats" )
    .upsert(
        source = function( q ) {
            q.from( "activeDirectoryUsers" )
                .select( [
                    "username",
                    "active",
                    "createdDate",
                    "modifiedDate"
                ] );
        },
        values = [ "username", "active", "createdDate", "modifiedDate" ],
        target = [ "username" ],
        update = [ "active", "modifiedDate" ]
    );
```

{% tabs %}
{% tab title="MySQL" %}
```sql
INSERT INTO `users`
    (`username`, `active`, `createdDate`, `modifiedDate`)
SELECT `username`, `active`, `createdDate`, `modifiedDate`
FROM `activeDirectoryUsers`
ON DUPLICATE KEY UPDATE
    `active` = VALUES(`active`),
    `modifiedDate` = VALUES(`modifiedDate`)
```
{% endtab %}

{% tab title="SQL Server" %}
```sql
MERGE [users] AS [qb_target]
USING (
    SELECT [username], [active], [createdDate], [modifiedDate]
    FROM [activeDirectoryUsers]
) AS [qb_src]
ON [qb_target].[username] = [qb_src].[username]
WHEN MATCHED THEN UPDATE
    SET [active] = [qb_src].[active],
        [modifiedDate] = [qb_src].[modifiedDate]
WHEN NOT MATCHED BY TARGET THEN INSERT
    ([username], [active], [createdDate], [modifiedDate])
VALUES ([username], [active], [createdDate], [modifiedDate]);
```
{% endtab %}

{% tab title="Postgres" %}
```sql
INSERT INTO "users"
("username", "active", "createdDate", "modifiedDate")
SELECT "username", "active", "createdDate", "modifiedDate"
FROM "activeDirectoryUsers"
ON CONFLICT ("username") DO UPDATE
    "active" = EXCLUDED."active",
    "modifiedDate" = EXCLUDED."modifiedDate"
```
{% endtab %}

{% tab title="Oracle" %}
```sql
MERGE INTO "USERS" "QB_TARGET"
USING (
    SELECT "USERNAME", "ACTIVE", "CREATEDADATE", "MODIFIEDDATE"
    FROM "ACTIVEDIRECTORYUSERS"
) "QB_SRC"
ON "QB_TARGET"."USERNAME" = "QB_SRC"."USERNAME"
WHEN MATCHED THEN UPDATE
    SET "ACTIVE" = "QB_SRC"."ACTIVE",
        "MODIFIEDDATE" = "QB_SRC"."MODIFIEDDATE"
WHEN NOT MATCHED THEN INSERT
    ("USERNAME", "ACTIVE", "CREATEDDATE", "MODIFIEDDATE")
    VALUES
    (
        "QB_SRC"."USERNAME",
        "QB_SRC"."ACTIVE",
        "QB_SRC"."CREATEDDATE",
        "QB_SRC"."MODIFIEDDATE"
    )
```
{% endtab %}
{% endtabs %}

## delete

| Name     | Type    | Required | Default | Description                                                                                                                                                   |
| -------- | ------- | -------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| id       | any     | `false`  |         | A convenience argument for \`where( "id", "=", arguments.id ). The query can be constrained by normal [WHERE](../building-queries/wheres.md) methods as well. |
| idColumn | string  | `false`  | `"id"`  | The name of the id column for the delete shorthand.                                                                                                           |
| options  | boolean | `false`  | `{}`    | Any additional `queryExecute` options.                                                                                                                        |
| toSql    | boolean | `false`  | `false` | If `true`, returns the raw SQL string instead of running the query. Useful for debugging.                                                                     |

Deletes all records that the query returns.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .where( "email", "foo" )
    .delete();
```
{% endcode %}

{% code title="MySQL" %}
```sql
DELETE FROM `users`
WHERE `email` = ?
```
{% endcode %}

The `id` argument is a convenience to delete a single record by id.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .delete( 1 );
```
{% endcode %}

{% code title="MySQL" %}
```sql
DELETE FROM `users`
WHERE `id` = ?
```
{% endcode %}

## returning

| Name    | Type            | Required | Default | Description                                                                                   |
| ------- | --------------- | -------- | ------- | --------------------------------------------------------------------------------------------- |
| columns | string \| array | `true`   |         | A single column, a list or columns, or an array of columns to return from the inserted query. |

{% hint style="danger" %}
`returning` is only supported in `PostgresGrammar,` `SqlServerGrammar, and SQLiteGrammar.` Using this method on unsupported grammars will result in an `UnsupportedOperation` exception.  Be aware that using this method constrains your grammar choices.
{% endhint %}

Specifies columns to be returned from the insert query.

<pre class="language-javascript" data-title="QueryBuilder"><code class="lang-javascript">query.from( "users" )
    .returning( "id" )
    .insert( {
        "email" = "foo",
        "name" = "bar"
<strong>    } );
</strong></code></pre>

{% tabs %}
{% tab title="SQL Server" %}
```sql
INSERT INTO [users] ([email], [name])
OUTPUT INSERTED.[id]
VALUES (?, ?)
```
{% endtab %}

{% tab title="Postgres" %}
```sql
INSERT INTO "users" ("email", "name")
VALUES (?, ?)
RETURNING "id"
```
{% endtab %}

{% tab title="SQLite" %}
```sql
INSERT INTO "users" ("email", "name")
VALUES (?, ?)
RETURNING "id"
```
{% endtab %}
{% endtabs %}

The `returning` function also applies to `update` and `delete` calls.

{% code title="QueryBuilder" %}
```javascript
query.table( "users" )
    .returning( [ "id", "modifiedDate" ] )
    .where( "id", 1 )
    .update( { "email": "john@example.com" } );
```
{% endcode %}

{% tabs %}
{% tab title="SQL Server" %}
```sql
UPDATE [users]
SET [email] = ?
OUTPUT INSERTED.[id], INSERTED.[modifiedDate]
WHERE [id] = ?
```
{% endtab %}

{% tab title="Postgres" %}
```sql
UPDATE "users"
SET "email" = ?
WHERE "id" = ?
RETURNING "id", "modifiedDate"
```
{% endtab %}

{% tab title="SQLite" %}
```sql
UPDATE "users"
SET "email" = ?
WHERE "id" = ?
RETURNING "id", "modifiedDate"
```
{% endtab %}
{% endtabs %}

<pre class="language-javascript" data-title="QueryBuilder"><code class="lang-javascript"><strong>query.table( "users" )
</strong>    .returning( "id" )
    .where( "active", 0 )
    .delete();
</code></pre>

{% tabs %}
{% tab title="SQL Server" %}
```sql
DELETE FROM [users]
OUTPUT DELETED.[id]
WHERE [active] = ?
```
{% endtab %}

{% tab title="Postgres" %}
```sql
DELETE FROM "users" WHERE "active" = ?
RETURNING "id"
```
{% endtab %}

{% tab title="SQLite" %}
```sql
DELETE FROM "users" WHERE "active" = ?
RETURNING "id"
```
{% endtab %}
{% endtabs %}

You can also use `raw` Expressions in a `returning` call.  This is especially useful for SQL Server returning both the old and new values from an `update` call.

{% code title="QueryBuilder" %}
```javascript
qb.from( "users" )
    .where( "id", 1 )
    .returningRaw( [
        "DELETED.modifiedDate AS oldModifiedDate",
        "INSERTED.modifiedDate AS newModifiedDate"
    ] )
    .update( { "email": "john@example.com" } );
```
{% endcode %}

{% tabs %}
{% tab title="SQL Server" %}
```sql
UPDATE [users]
SET [email] = ?
OUTPUT
    DELETED.modifiedDate AS oldModifiedDate,
    INSERTED.modifiedDate AS newModifiedDate
WHERE [id] = ?
```
{% endtab %}
{% endtabs %}
