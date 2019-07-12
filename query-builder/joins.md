# Joins

## Inner Join Clause

The query builder may also be used to write join statements. To perform a basic "inner join", you may use the `join` method on a query builder instance. The first argument passed to the `join` method is the name of the table you need to join to, while the remaining arguments specify the column constraints for the join. Of course, as you can see, you can join to multiple tables in a single query:

```javascript
//qb
var getResults = query.from('blogs')
    .join('users', 'users.ID', '=', 'blogs.FK_usersID')
    .get();
writeDump(getResults);

//sql
SELECT users.name,blogs.title,blogs.ID as blogID FROM `blogs` INNER JOIN `users` ON `users`.`ID` = `blogs`.`FK_usersID`
```

### Joining using raw SQL

Sometimes you need more control over your `join` clause in order to add grammar specific instructions, such as adding SQL Server table hints to your queries.

If you need complete control over your `join` clause you can use the `joinRaw()` method

```javascript
//qb
var getResults = query.from('blogs')
    .joinRaw('[users] AS u (nolock)', 'u.ID', '=', 'blogs.FK_usersID')
    .get();
writeDump(getResults);

//sql
SELECT * FROM [blogs] INNER JOIN [users] AS u (nolock) ON [u].[ID] = [blogs].[FK_usersID]
```

Since the `joinRaw()` takes your string verbatim, it's important that you make sure your SQL declaration is escaped properly. Failure to properly escape your table names may result in SQL errors.

> _NOTE:_ Using the `joinRaw()` will most likely tie your code to a specific database, so think carefully before using the `joinRaw()` method if you want your project to be database agnostic.
>
> _NOTE:_ All of the `join` methods have a `*Raw` equivalent method. This means you can use `leftJoinRaw()`, `rightJoinRaw()`, etc.

### Complex \(multi-conditional\) Join Clause

For a compound join clause, pass in the name of the table as the first argument \(just as before\) but instead of passing the remaining arguments describing the single join clause, we'll pass a single closure with a `joinClause` argument. Consider a \(contrived\) example where our `users` and `blogs` had to match not only `ID` but also `type`:

```javascript
//qb
var getResults = query.from( "blogs" )
    .join( "users", function( j ) {
        j.on( "users.ID", "=", "blogs.FK_usersID" )
            .on( "users.type", "=", "blogs.type" );
    } )
    .get();

//sql
SELECT users.name,blogs.title,blogs.ID as blogID FROM `blogs` INNER JOIN `users` ON `users`.`ID` = `blogs`.`FK_usersID` AND `users`.`type` = `blogs`.`type`
```

## Left/Right Join Clause

If you would like to perform a "left/right join" instead of an "inner join", use the `leftJoin` / `rightJoin` method. The `leftJoin` / `rightJoin` method has the same signature as the `join` method:

```javascript
//qb (leftJoin)
var getResults = query.from('blogs')
    .leftJoin('users', 'users.ID', '=', 'blogs.FK_usersID')
    .get();
writeDump(getResults);

//qb (rightJoin)
var getResults = query.from('blogs')
    .rightJoin('users', 'users.ID', '=', 'blogs.FK_usersID')
    .get();
writeDump(getResults);

//sql (leftJoin)
SELECT * FROM `blogs` LEFT JOIN `users` ON `users`.`ID` = `blogs`.`FK_usersID`

//sql (rightJoin)
SELECT * FROM `blogs` RIGHT JOIN `users` ON `users`.`ID` = `blogs`.`FK_usersID`
```

## Cross Join Clause

To perform a "cross join" use the `crossJoin` method with the name of the table you wish to cross join to. Cross joins generate a cartesian product between the first table and the joined table:

```javascript
var getResults = query.from('users')
    .crossJoin('departments', 'departments.ID', '=', 'users.FK_departmentID')
    .get();
writeDump(getResults);
```

## Joining using Derived Tables

Complex queries often contain derived tables, which are temporal, subqueries defined inline within your SQL.

To join your main table to a derived table you can use the `joinSub()` methods. Each `join` method has a corresponding "sub" method which you can use when you need to use a derived table \(i.e. `leftJoinSub()`, `rightJoinSub()`, etc\).

These functions differ slightly than the normal `join` methods, because the first two arguments specify:

* The alias to use for the derived table \(which is how you reference your query\)
* Either a QueryBuilder instances or closure defining the subquery

```javascript
//qb
var getResults = query
    .from('blogs')
    .joinSub('u', function (q){
        q
            .from('users')
            .where('disabled', 0)
        ;
    }, 'u.ID', '=', 'blogs.FK_usersID')
    .get();
writeDump(getResults);

//sql
SELECT * FROM `blogs` INNER JOIN (SELECT * FROM `users` WHERE `disabled` = 0) AS `u` ON `u`.`ID` = `blogs`.`FK_usersID`
```

