# Common Table Expressions (CTEs)

Common Table Expressions (CTEs) are powerful SQL concept that allow you to create re-usable temporal resultset, which can be referenced as a table within your SQL. CTEs are available in many common database engines and are available in latest versions of all of the support grammars.

CTEs come in two basic types:

* **Non-recursive** — These are statements that do not reference themselves, in simplified terms they are like a derived table that can be referenced by a user-defined name.
* **Recursive** — Recursive CTEs reference themselves and are generally used for creating hierarchical data—such as creating a parent/child relationship within a table. 

While all of the grammars currently support CTEs, there is enough difference between the various databases implementations of CTEs that unless your CTEs are fairly basic, using CTEs within your project will most likely tie your project to a specific database, unless you account for the differences in your code.

However, CTEs are can be extremely useful to solve certain use cases.

To add CTEs to your queries, you have two methods available:

* `with()` — Allows you to define a non-recursive CTE.
* `withRecursive()` — Allows you to define a recursive CTE.

> *NOTE:* Some database engines require the `recursive` keyword is implemented anytime at least one of your CTEs is recursive, but some database engines (e.g. SQL Server and Oracle) do not require the keyword. For engines that do not require the `recursive` keyword the grammar will manage adding the keyword if necessary. If your query does use recursion, you should always use the `withRecursive()` method to avoid issues with other grammars.

## Simple CTE using a closure

Building a CTE is as easy as using the `with()` method with a closure:

```javascript
// qb
var getResults = query
	.with('UserCTE', function (q){
		q
			.select('fName as firstName', 'lName as lastName')
			.from('users')
			.where('disabled', 0)
		;
	})
	.from('UserCTE')
	.get();
writeDump(getResults);

// sql
WITH `UserCTE` AS (
	SELECT
		`fName` as `firstName`,
		`lName` as `lastName`
	FROM `users`
	WHERE `disabled` = 0
) SELECT * FROM `UserCTE`
```


## Simple CTE using a QueryBuilder instance

Alternatively, you can use a QueryBuilder instance instead of a closure:

```javascript
// qb
var cte = query
	.select('fName as firstName', 'lName as lastName')
	.from('users')
	.where('disabled', 0)
;

var getResults = query
	.with('UserCTE', cte)
	.from('UserCTE')
	.get();
writeDump(getResults);

// sql
WITH `UserCTE` AS (
	SELECT
		`fName` as `firstName`,
		`lName` as `lastName`
	FROM `users`
	WHERE `disabled` = 0
)
SELECT * FROM `UserCTE`
```


## Multiple CTEs

A single query can reference multiple CTEs:

```javascript
// qb
var getResults = query
	.with('UserCTE', function (q){
		q
			.select('id', 'fName as firstName', 'lName as lastName')
			.from('users')
			.where('disabled', 0)
		;
	})
	.with('BlogCTE', function (q){
		q
			.from('blogs')
			.where('disabled', 0)
		;
	})
	.from('BlogCTE as b')
	.join('UserCTE as u', 'b.Creator', 'u.id')
	.get();
writeDump(getResults);

// sql
WITH `UserCTE` AS (
	SELECT
		`id`,
		`fName` as `firstName`,
		`lName` as `lastName`
	FROM `users`
	WHERE `disabled` = 0
),
`BlogCTE` AS (
	SELECT *
	FROM `blogs`
	WHERE `disabled` = 0
)
SELECT *
FROM `BlogCTE` AS `b`
INNER JOIN `UserCTE` AS `u`
ON `b`.`Creator` = `u`.`id`
```


## Recursive CTEs

> **IMPORTANT** — The way the SQL in a recursive CTEs are written, using them in your code is likely to lock in you in to a specific database engine, unless you structure your code to build the correct SQL based on the current grammar being used.

Here is an example of building a recursive CTE using SQL Server which would return all parent/child rows and show their generation/level depth:

```javascript
// qb
var getResults = query
	.withRecursive('Hierarchy', function (q){
		q
			// get the parent rows only
			.select('Id', 'ParentId', 'Name', q.raw('0 AS [Generation]'))
			.from('Sample')
			.whereNull('ParentId')
			// use recursion to join the child rows to their parents
			.unionAll(function (q){
				q
					.select('child.Id', 'child.ParentId', 'child.Name', q.raw('[parent].[Generation] + 1'))
					.from("Sample as child)
					.join("Hierarchy as parent", "child.ParentId", "parent.Id")
				;
			})
		;
	}, ['Id', 'ParentId', 'Name', 'Generation'])
	.from('Hierarchy')
	.get();
writeDump(getResults);

// sql
;WITH [Hierarchy] ([Id], [ParentId], [Name], [Generation]) AS (SELECT [Id], [ParentId], [Name], 0 AS [Generation] FROM [Sample] WHERE [ParentId] IS NULL UNION ALL SELECT [child].[Id], [child].[ParentId], [child].[Name], [parent].[Generation] + 1 FROM [Sample] AS [child] INNER JOIN [Hierarchy] AS [parent] ON [child].[ParentId] = [parent].[Id]) SELECT * FROM [Hierarchy]
```

