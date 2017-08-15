## Updates

Of course, in addition to inserting records into the database, the query builder can also update existing records using the `update` method. The `update` method, like the `insert` method, accepts an array of column and value pairs containing the columns to be updated. You may constrain the `update` query using `where` clauses:


```
//qb
var addRecords = query.from( "users" )
	.whereID( 10 )
	.update(  { "name" = "Roberto", "email" = "roberto@test.com", "age" = 55 });
writeDump(addRecords);

//sql
UPDATE `users` SET `age` = 55, `email` = `roberto@test.com`, `name` = `Roberto` WHERE `ID` = 10
```
