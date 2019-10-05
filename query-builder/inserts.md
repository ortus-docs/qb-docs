# Inserts

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

