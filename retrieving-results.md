## Retrieving Results

#### Retrieving All Rows From A Table
```
var getAllResults = query.from('users').get();
writeDump(getAllResults);
```

The `get` method returns an `Array` (by default) of Structs.

#### Retrieving A Single Row / Column From A Table

If you just need to retrieve a single row from the database table, you may use the `first` method. This method will return a single record:

```
var getResults = query.from('users').first();
writeDump(getResults);
```

If you don't even need an entire row, you may extract a single value from a record using the `value` method. This method will return the value of the column directly:

```
var getResults = query.from('users').values('email');
writeDump(getResults);
```


#### Retrieving A List Of Column Values

If you would like to retrieve a Collection containing the values of a single column, you may use the `pluck` method. In this example, we'll retrieve a Collection of role titles:

```
**need sample here**
```

You may also specify a custom key column for the returned Collection:

```
**need sample here**
```



