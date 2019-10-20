# Unions

The query builder also lets you create union statements on your queries. When merging multiple queries together using a union statement, there are two methods you can use merge the queries together:

* union\(\) — This method builds a SQL statement using the ANSI SQL `union` clause which combines two SQL queries into a single resultset containing all the matching rows. The two queries _must_ have the same defined columns and compatible data types or the SQL engine will generate an error. The `union` clause only returns unique rows.
* unionAll\(\) — This builds a SQL statement using the `union all` clause. This is the same as `union` but includes duplicate rows. 

The `union` methods take either a Query Builder instance or a closure, which you use to define a new QueryBuilder instance.

Union statements are added in the order in which the `union` methods are invoked, but the `union` statements can be in any order in your API call stack. This means you can safely declare your `union` method calls before the `select`, `from` and `orderBy` calls on the source Query Builder instance.

> _IMPORTANT:_ The QueryBuilder instances passed to a `union` statement _cannot_ contain a defined order. Any use of the `orderBy()` method on the unioned QueryBuilder instances will result in an exception. To order the results, add an `orderBy()` call to the parent source Query Builder instance.

## Simple `union` using a callback

The easiest way to combine union the results of multiple queries is by using a callback to the `union` methods:

```javascript
//qb
var getResults = query
        .select('title')
        .from('blogs')
        .whereIn('id', [1, 2, 3])
        .union(function (q){
            q
                .select('title')
                .from('blogs-archive')
                .whereIn('id', [1, 2, 3])
            ;
        })
    .get();
writeDump(getResults);

//sql
SELECT `title` FROM `blogs` WHERE `id` IN (1, 2, 3) UNION SELECT `title` FROM `blogs-archive` WHERE `id` IN (1, 2, 3)
```

## Using a Query Builder instance

Alternatively, you can use another Query Builder instance and pass it to the `union` methods:

```javascript
var unionQB = query
    .select('title')
    .from('blogs-archive')
    .whereIn('id', [1, 2, 3])
;

//qb
var getResults = query
        .select('title')
        .from('blogs')
        .whereIn('id', [1, 2, 3])
        .union(unionQB)
    .get();
writeDump(getResults);

//sql
SELECT `title` FROM `blogs` WHERE `id` IN (1, 2, 3) UNION SELECT `title` FROM `blogs-archive` WHERE `id` IN (1, 2, 3)
```

## `union all`

If you want to make sure that all duplicate rows are returned, use the `unionAll()` method instead of `union()`:

```javascript
//qb
var getResults = query
        .select('title')
        .from('blogs')
        .whereIn('id', [1, 2, 3])
        .unionAll(function (q){
            q
                .select('title')
                .from('blogs-archive')
                .whereIn('id', [1, 2, 3])
            ;
        })
    .get();
writeDump(getResults);

//sql
SELECT `title` FROM `blogs` WHERE `id` IN (1, 2, 3) UNION ALL SELECT `title` FROM `blogs-archive` WHERE `id` IN (1, 2, 3)
```

## Ordering `union` queries

To order a `union` query, only the parent query object can contain an `orderBy()` directive. If any of the Query Builder instances passed to a `union` method contain an `orderBy` directive an exception will be thrown when you attempt to either execute the query or generate the SQL.

The follow shows how to order the results:

```javascript
//qb
var getResults = query
        .select('title')
        .from('blogs')
        .whereIn('id', [1, 2, 3])
        .union(function (q){
            q
                .select('title')
                .from('blogs-archive')
                .whereIn('id', [1, 2, 3])
            ;
        })
        .orderBy("title", "desc")
    .get();
writeDump(getResults);

//sql
SELECT `title` FROM `blogs` WHERE `id` IN (1, 2, 3) UNION SELECT `title` FROM `blogs-archive` WHERE `id` IN (1, 2, 3) ORDER BY `title` DESC
```

> _NOTE:_ The `orderBy()` call does have to be after the calls to the `union` methods.

## Multiple `union` statements

Your query can contain multiple `union` methods:

```javascript
//qb
var getResults = query
        .select('title')
        .from('blogs')
        .whereIn('id', [1, 2, 3])
        // order can be *before* union statements
        .orderBy("title", "desc")
        .unionAll(function (q){
            q
                .select('title')
                .from('blogs-archive-1990-decade')
                .whereIn('id', [1, 2, 3])
            ;
        })
        .unionAll(function (q){
            q
                .select('title')
                .from('blogs-archive-2000-decade')
                .whereIn('id', [1, 2, 3])
            ;
        })
    .get();
writeDump(getResults);

//sql
SELECT `title` FROM `blogs` WHERE `id` IN (1, 2, 3) UNION ALL SELECT `title` FROM `blogs-archive-1990-decade` WHERE `id` IN (1, 2, 3) UNION ALL SELECT `title` FROM `blogs-archive-2000-decade` WHERE `id` IN (1, 2, 3) ORDER BY `title` DESC
```

