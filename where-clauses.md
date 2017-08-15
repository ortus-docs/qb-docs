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
##### LARAVEL PHP EQUIVELANT
```
$users = DB::table('users')
->whereNull('updated_at')
->get();
```

The `whereNotNull` method verifies that the column's value is not `NULL`:

##### LARAVEL PHP EQUIVELANT
```
$users = DB::table('users')
->whereNotNull('updated_at')
->get();
```

**whereDate / whereMonth / whereDay / whereYear**

The `whereDate` method may be used to compare a column's value against a date:

$users = DB::table('users')
->whereDate('created_at', '2016-12-31')
->get();

The `whereMonth` method may be used to compare a column's value against a specific month of a year:

$users = DB::table('users')
->whereMonth('created_at', '12')
->get();

The `whereDay` method may be used to compare a column's value against a specific day of a month:

$users = DB::table('users')
->whereDay('created_at', '31')
->get();

The `whereYear` method may be used to compare a column's value against a specific year:

$users = DB::table('users')
->whereYear('created_at', '2016')
->get();

**whereColumn**

The `whereColumn` method may be used to verify that two columns are equal:

$users = DB::table('users')
->whereColumn('first_name', 'last_name')
->get();

You may also pass a comparison operator to the method:

$users = DB::table('users')
->whereColumn('updated_at', '>', 'created_at')
->get();

The `whereColumn` method can also be passed an array of multiple conditions. These conditions will be joined using the `and` operator:

$users = DB::table('users')
->whereColumn([
['first_name', '=', 'last_name'],
['updated_at', '>', 'created_at']
])->get();

