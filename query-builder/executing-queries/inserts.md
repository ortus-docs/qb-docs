# Inserts, Updates, and Deletes

## Inserts

The query builder also provides an `insert` method for inserting records into the database table. The `insert` method accepts an array of column names and values:

```javascript
//qb
var addRecords = query.from( "users" )
        .insert( values = { "name" = "Robert", "email" = "robert@test.com", "age" = 55 } );
writeDump(addRecords);

//sql
INSERT INTO `users` (`age`, `email`, `name`) VALUES (55, `robert@test.com`, `Robert`)
```

You may even insert several records into the table with a single call to `insert` by passing an array of structs. Each struct represents a row to be inserted into the table:

```javascript
//qb
var addRecords = query.from( "users" )
.insert( values = [
        { "name" = "Robert", "email" = "robert@test.com", "age" = 55 },
        { "name" = "Jessica", "email" = "jessica@test.com", "age" = 31 },
        { "name" = "Ross", "email" = "ross@test.com", "age" = 9 }
    ] );
writeDump(addRecords);

//sql
INSERT INTO `users` (`age`, `email`, `name`)
VALUES (55, `robert@test.com`, `Robert`),
        (31, `jessica@test.com`, `Jessica`),
        (9, `ross@test.com`, `Ross`)
```

You can also insert records by strong typing them just like using `cfqueryParam`. Just adhere to the same syntax: `{ value : "", cfsqltype : "" }` \([https://cfdocs.org/cfqueryparam](https://cfdocs.org/cfqueryparam)\)

```javascript
//qb
var addRecords = query
        .from( "users" )
        .insert( values = { 
                "name" = "Robert", 
                "email" = "robert@test.com", 
                "age" = { value : 55, cfsqltype : "integer" },
                "createdDate" = { value : now(), cfsqltype : "timestamp" }
        } );
writeDump(addRecords);
```

## Returning

Certain grammars have the ability to return values from an insert statement. That can be useful if you use your built-in database functions to generate primary keys that you want to retrieve.

```javascript
// qb
var addRecords = query.from( "users" )
    .returning( "id" )
    .insert( { "name" = "Robert", "email" = "robert@test.com", "age" = 55 } );
writeDump(addRecords);

// Postgres
INSERT INTO "users" ("age", "email", "name")
VALUES (55, "robert@test.com", "Robert")
RETURNING "id"

// MSSQL
INSERT INTO [users] ([age], [email], [name])
OUTPUT INSERTED.[id]
VALUES (55, "robert@test.com", "Robert")
```

If you attempt to use `returning` on grammars that do not support it, you will recieve a `UnsupportedOperation` exception.

## Updates

Of course, in addition to inserting records into the database, the query builder can also update existing records using the `update` method. The `update` method, like the `insert` method, accepts an array of column and value pairs containing the columns to be updated. You may constrain the `update` query using `where` clauses:

```text
//qb
var addRecords = query.from( "users" )
    .whereID( 10 )
    .update(  { "name" = "Roberto", "email" = "roberto@test.com", "age" = 55 });
writeDump(addRecords);

//sql
UPDATE `users` SET `age` = 55, `email` = `roberto@test.com`, `name` = `Roberto` WHERE `ID` = 10
```

You can also use Expressions inside an update statement:

```javascript
query.from( "posts" )
    .whereID( 10 )
    .update( { "likes" = query.raw( "likes + 1" ) } );
    
// SQL:      UPDATE `posts` SET `likes` = likes + 1 WHERE `ID` = ?
// Bindings: [ 10 ]
```

## Deletes

The query builder may also be used to delete records from the table via the `delete` method. You may constrain `delete` statements by adding `where` clauses before calling the `delete` method:

```text
//qb
var deleteRecords = query.from( "users" )
    .whereID( 10 )
    .delete();
writeDump(deleteRecords);

//sql
DELETE FROM `users` WHERE `ID` = 10;
```

This utilizes the where clause on a column other than the ID:

```text
//qb
var deleteRecords = query.from( "users" )
    .where( 'age', '>', 50 )
    .delete();
writeDump(deleteRecords);

//sql
DELETE FROM `users` WHERE `age` > 50
```

