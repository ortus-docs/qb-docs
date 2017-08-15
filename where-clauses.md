## Where Clauses

#### Simple Where Clauses

You may use the `where` method on a query builder instance to add `where` clauses to the query. The most basic call to `where` requires three arguments. The first argument is the name of the column. The second argument is an operator, which can be any of the database's supported operators. Finally, the third argument is the value to evaluate against the column.

For example, here is a query that verifies the value of the "age" column is greater than or equal to 18:
```
var getResults = query.from('users')
    .where('age','>=','18')
    .get();
writeDump(getResults);

```


For convenience, if you simply want to verify that a column is **equal** to a given value, you may pass the value directly as the second argument to the `where` method:
```
var getResults = query.from('users')
    .where('age','18')
    .get();
writeDump(getResults);

```


Of course, you may use a variety of other operators when writing a `where` clause:


```
var getResults = query.from('users')
    .where('age','>=','18')
    .get();
writeDump(getResults);

var getResults = query.from('users')
    .where('age','<>','18')
    .get();
writeDump(getResults);

var getResults = query.from('users')
    .where('name','like','A%')
    .get();
writeDump(getResults);

```

You may also pass an array of conditions to the `where` function:

##### LARAVEL PHP EQUIVELANT
```
$users = DB::table('users')->where([
['status', '=', '1'],
['subscribed', '<>', '1'],
])->get();
```

#### Or Statements

You may chain where constraints together as well as add `or` clauses to the query. The `orWhere` method accepts the same arguments as the `where` method:


```
var getResults = query.from('users')
    .where('name','like','A%')
    .orWhere('age','>','30')
    .get();
writeDump(getResults);
```
#### Additional Where Clauses

**whereBetween**

The `whereBetween` method verifies that a column's value is between two values:

```
var getResults = query.from('users')
    .whereBetween('age','18','21')
    .get();
writeDump(getResults);
```
**whereNotBetween**

The `whereNotBetween` method verifies that a column's value lies outside of two values:

```
var getResults = query.from('users')
    .whereNotBetween('age','18','21')
    .get();
writeDump(getResults);
```


**whereIn / whereNotIn**

The `whereIn` method verifies that a given column's value is contained within the given array:

```
var getResults = query.from('users')
    .whereIn('age','17,18,19,20,21')
    .get();
writeDump(getResults);
```

The `whereNotIn` method verifies that the given column's value is **not** contained in the given array:


```
var getResults = query.from('users')
    .whereNotIn('age','17,18,19,20,21')
    .get();
writeDump(getResults);
```


**whereNull / whereNotNull**

The `whereNull` method verifies that the value of the given column is `NULL`:

```
var getResults = query.from('users')
    .whereNull('modifiedDate')
    .get();
writeDump(getResults);
```

The `whereNotNull` method verifies that the column's value is not `NULL`:


```
var getResults = query.from('users')
.whereNotNull('modifiedDate')
.get();
writeDump(getResults);
```

**whereDate / whereMonth / whereDay / whereYear**

The `whereDate` method may be used to compare a column's value against a date:

```
var getResults = query.from('users')
    .whereDate('modifiedDate','2017-08-15')
    .get();
writeDump(getResults);
```

The `whereMonth` method may be used to compare a column's value against a specific month of a year:

```
var getResults = query.from('users')
    .whereDate('modifiedDate','12')
    .get();
writeDump(getResults);
```



The `whereDay` method may be used to compare a column's value against a specific day of a month:

```
var getResults = query.from('users')
    .whereDate('modifiedDate','15')
    .get();
writeDump(getResults);
```



The `whereYear` method may be used to compare a column's value against a specific year:

```
var getResults = query.from('users')
    .whereDate('modifiedDate','2017')
    .get();
writeDump(getResults);
```



**whereColumn**

The `whereColumn` method may be used to verify that two columns are equal:

```
var getResults = query.from('users')
    .whereColumn('modifiedDate','createdDate')
    .get();
writeDump(getResults);
```

You may also pass a comparison operator to the method:

```
var getResults = query.from('users')
    .whereColumn('modifiedDate','<>','createdDate')
    .get();
writeDump(getResults);
```



