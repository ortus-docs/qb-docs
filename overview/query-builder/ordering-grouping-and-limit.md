# Ordering, Grouping & Limit

## orderBy

The `orderBy` method allows you to sort the result of the query by a given column. The first argument to the `orderBy` method should be the column you wish to sort by, while the second argument controls the direction of the sort and may be either `asc` or `desc`:

```text
//qb
var getResults = query.from('users')
    .where('age','>=','18')
    .orderBy('modifiedDate','desc')
    .get();
writeDump(getResults);

//sql
SELECT * FROM `users` WHERE `age` >= 18 ORDER BY `modifiedDate` DESC
```

{% hint style="info" %}
If you want to order by multiple columns, you can call `orderBy` multiple times.
{% endhint %}

## groupBy / having

The `groupBy` and `having` methods may be used to group the query results. The `having` method's signature is similar to that of the `where` method:

```text
//qb
var getResults = query.from('users')
    .groupBy('FK_departmentID')
    .having('age','>','21')
    .orderBy('age','desc')
    .get();
writeDump(getResults);

//sql
SELECT * FROM `users` GROUP BY `FK_departmentID` HAVING `age` > 21 ORDER BY `age` DESC
```

## take / limit

To limit the number of results returned from the query, you may use the `take` method:

```text
//qb
var getResults = query.from('users')
    .where('age','>=','18')
    .orderBy('modifiedDate','desc')
    .take(5)
    .get();
writeDump(getResults);

//sql
SELECT * FROM `users` WHERE `age` >= ? ORDER BY `modifiedDate` DESC LIMIT 5
```

Alternatively, you may use the `limit` method:

```text
//qb
var getResults = query.from('users')
    .where('age','>=','18')
    .orderBy('modifiedDate','desc')
    .limit(5)
    .get();
writeDump(getResults);

//sql
SELECT * FROM `users` WHERE `age` >= ? ORDER BY `modifiedDate` DESC LIMIT 5
```

