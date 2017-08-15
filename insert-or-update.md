## Insert or Update

The query builder also provides an `updateOrInsert` method for updating records into a database OR inserting records into the database table if the record does not exist. The `updateOrInsert` method accepts an array of column names and values:


```
//qb
var addRecords = query.from( "users" )
	.whereID( 10 )
	.updateOrInsert(  { "name" = "Roberto", "email" = "roberto@test.com", "age" = 55 });
writeDump(addRecords);

//sql - if the record exists based off `where` clauses (in this case `whereID`):
UPDATE `users` SET `age` = 55, `email` = `roberto@test.com`, `name` = `Roberto` WHERE `ID` = 10

//sql - if the record does NOT exists:
INSERT INTO `users` (`age`, `email`, `name`) VALUES (55, 'roberto@test.com', 'Roberto')


```
