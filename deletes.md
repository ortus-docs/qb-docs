## Deletes

The query builder may also be used to delete records from the table via the `delete` method. You may constrain `delete` statements by adding `where` clauses before calling the `delete` method:

```
//qb
var deleteRecords = query.from( "users" )
    .whereID( 10 )
    .delete();
writeDump(deleteRecords);

//sql
DELETE FROM `users` WHERE `ID` = 10;
```

This utilizes the where clause on a column other than the ID:

```
//qb
var deleteRecords = query.from( "users" )
    .where( 'age', '>', 50 )
    .delete();
writeDump(deleteRecords);

//sql
DELETE FROM `users` WHERE `age` > 50
```
