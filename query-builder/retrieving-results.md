# Retrieving Results

Usage\#\# Retrieving Results

## Retrieving All Rows From A Table

```javascript
// qb
var getAllResults = query.from( "users" ).get();
writeDump( getAllResults );

// sql
// select * from users

// more qb examples
var getAllResults = query.from( "users" )
    .get(
        columns = [ "Id", "Name" ],
        options = { datasource = "myAdditionalDatasource" }
    );
```

The `get` method returns an `Array` of `Structs` by default. Both `columns` and `options` are optional.

```javascript
public any function get( any columns, struct options = {} )
```

## Retrieving A Single Row / Column From A Table

If you just need to retrieve a single row from the database table, you may use the `first` method. This method will return a single record \(a `Struct` by default\). If no row is found an empty `Struct` will be returned by default.

```javascript
//qb
var getResults = query.from('users')
    .first();
writeDump(getResults);

//sql
select * from users limit(1)
```

```javascript
public any function first( struct options = {} )
```

If you don't even need an entire row, you may extract a single value from each record using the `values` method. This method will return the value of the column directly:

```javascript
query.from( "users" ).values( "email" );
// [ "john@example.com", "jane@example.com", "jim@example.com", ... ]
```

```javascript
public any function values( required string column, struct options = {} );
```

If you only need a single column for the first record returned, use the `value` function:

```javascript
query.from( "users" ).value( "email" );
// "john@example.com"
```

```javascript
public any function value( required string column, struct options = {} );
```

## Options parameter: Retrieving results from alternative datasources

In application.cfc you can specify your default datasource which will be used by qb. If you want to retrieve data from other datasources you can specify this in all retrieval functions by using the extra options parameter such as:

```javascript
//qb
var getAllResults = query.from('users')
    .get( options={ datasource= 'MyOtherDatasourceName'} );
writeDump(getAllResults);
```

If you also want to use a non-default SQL Grammar you have to specify this when creating your querybuilder, e.g

```javascript
var query =  wirebox.getInstance('QueryBuilder@qb')
    .setGrammar( wirebox.getInstance('MSSQLGrammar@qb') );
```

