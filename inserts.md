## Inserts

The query builder also provides an `insert` method for inserting records into the database table. The `insert` method accepts an array of column names and values:


```
//qb
var addRecords = query.from( "users" )
.insert( values = { "name" = "Robert", "email" = "robert@test.com", "age" = 55 } );
writeDump(addRecords);

//sql
INSERT INTO `users` (`age`, `email`, `name`) VALUES (55, `robert@test.com`, `Robert`)
```
You may even insert several records into the table with a single call to `insert` by passing an array of arrays. Each array represents a row to be inserted into the table:


```
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
