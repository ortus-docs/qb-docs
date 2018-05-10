# Retrieving Results

Usage\#\# Retrieving Results

## Retrieving All Rows From A Table

```text
//qb
var getAllResults = query.from('users')
    .get();
writeDump(getAllResults);

//sql
select * from users
```

The `get` method returns an `Array` of `Structs` by default.

## Retrieving A Single Row / Column From A Table

If you just need to retrieve a single row from the database table, you may use the `first` method. This method will return a single record:

```text
//qb
var getResults = query.from('users')
    .first();
writeDump(getResults);

//sql
select * from users limit(1)
```

If you don't even need an entire row, you may extract a single value from a record using the `value` method. This method will return the value of the column directly:

```text
//qb
var getResults = query.from('users')
    .values('email');
writeDump(getResults);
```

