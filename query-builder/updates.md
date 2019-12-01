# Updates

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

### Updating Null values

Null values can be inserted by using queryparam syntax:  


```javascript
query.from("user")
		.whereId( 10 )
		.update( {
			manager_FK = { value = "", null=true },
		} )
```

if you are using Lucee with full null support the following \(easier\) syntax is also allowed:

```javascript
query.from("user")
		.whereId( 10 )
		.update( {
			manager_FK = { value = null },
		} )
```

