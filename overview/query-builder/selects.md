# Selects

## Specifying A Select Clause

Of course, you may not always want to select all columns from a database table. Using the `from` method, you can specify a custom `from` clause for the query:

```javascript
var getResults = query.from('users')
    .get('name,email,age');
writeDump(getResults);
```

Individual columns can contain fully-qualified names \(i.e. "some\_table.some\_column"\), fully-qualified names with table aliases \(i.e. "alias.some\_column"\), and even set column aliases themselves \(i.e. "some\_column AS c"\). Columns can be a single column, a list or columns \(comma-separated\), or an array of columns.

```javascript
var getResults = query.from('users')
    .get('name as myAccountName,users.email,age');
writeDump(getResults);
```

```javascript
var getResults = query.from('users as myTableAlias')
    .get( columns = ['name as myAccountName' ,'myTableAlias.email' ,'age'], options= { datasource='myOtherDatasource'} );
writeDump(getResults);
```

The `distinct` method allows you to force the query to return distinct results:

```javascript
var getResults = query.from('users')
    .distinct('email');
writeDump(getResults);
```

If you already have a query builder instance and you wish to add a column to its existing select clause, you may use the `addSelect` method:

```javascript
var getResults = query.from('users')
    .where('age','>=','18');
getResults = getResults.addSelect('name,email,age').get();
writeDump(getResults);
```

## SelectRaw

If you need to drop into raw SQL when specifying a column to retrieve, you can use `selectRaw()`.

```javascript
var getResults = query.from('users')
    .where('age','>=','18')
    .select('id,age,username')
    .selectRaw('CONCAT(fname, " ", lname) fullName')
    .get();
writeDump(getResults);
```

If you so choose, you could also use QB's `raw()` function, which is what `selectRaw()` uses under the hood.

```javascript
var getResults = query.from('users')
    .where('age','>=','18')
    .select( query.raw('id,age,username,CONCAT(fname, " ", lname) fullName') )
    .get();
writeDump(getResults);
```
