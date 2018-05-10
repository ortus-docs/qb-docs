# Selects

## Specifying A Select Clause

Of course, you may not always want to select all columns from a database table. Using the `from` method, you can specify a custom `from` clause for the query:

```text
var getResults = query.from('users')
    .get('name,email,age');
writeDump(getResults);
```

The `distinct` method allows you to force the query to return distinct results:

```text
var getResults = query.from('users')
    .distinct('email');
writeDump(getResults);
```

If you already have a query builder instance and you wish to add a column to its existing select clause, you may use the `addSelect` method:

```text
var getResults = query.from('users')
    .where('age','>=','18');
getResults = getResults.addSelect('name,email,age').get();
writeDump(getResults);
```

